# Triangle ( Writeup )

![image.png](<../../.gitbook/assets/image (1) (2).png>)

**During the assessment, I reviewed the public-facing source code using the browser’s `view-source` feature. Within the HTML comments, I discovered a developer note indicating the presence of an unprotected backup file and an authentication-related script (`google2fa.php`) that may still exist on the server. The comment was as follows:**

```html
<!-- Dev team 2: TODO: Implement google2fa.php for auth and don't forget to clean up the bak files post debugging before release -->
```

**This comment suggests that debugging artifacts or backup files (`.bak`) may have been left behind in the production environment, potentially exposing sensitive information or leading to source code disclosure. It also reveals internal implementation details about multi-factor authentication components that may be accessible directly if not properly restricted**

![image.png](<../../.gitbook/assets/image (2) (2).png>)

**After identifying the developer comment referencing `.bak` files, I performed targeted fuzzing on the application to check for accessible backup files left behind in the production environment. The fuzzing results confirmed the presence of two publicly accessible backup files: `login.php.bak` and `google2fa.php.bak`.**

**Both files were directly downloadable over HTTP without authentication. I retrieved them using `wget` to analyze their contents:**

```bash
wget <http://15.206.47.5:8080/login.php.bak>
wget <http://15.206.47.5:8080/google2fa.php.bak>
```

**The server responded with HTTP 200 OK, and both files were successfully downloaded, confirming that sensitive source code was being exposed through leftover backup artifacts. This constitutes a severe information disclosure vulnerability, as attackers can obtain core authentication logic and internal implementation details simply by accessing these `.bak` files.**

![image.png](<../../.gitbook/assets/image (3) (2).png>)

**`google2fa.php.bak`**

```jsx
<?

class Google2FA {

	const keyRegeneration 	= 30;
	const otpLength		= 6;

	private static $lut = array(
		"A" => 0,	"B" => 1,
		"C" => 2,	"D" => 3,
		"E" => 4,	"F" => 5,
		"G" => 6,	"H" => 7,
		"I" => 8,	"J" => 9,
		"K" => 10,	"L" => 11,
		"M" => 12,	"N" => 13,
		"O" => 14,	"P" => 15,
		"Q" => 16,	"R" => 17,
		"S" => 18,	"T" => 19,
		"U" => 20,	"V" => 21,
		"W" => 22,	"X" => 23,
		"Y" => 24,	"Z" => 25,
		"2" => 26,	"3" => 27,
		"4" => 28,	"5" => 29,
		"6" => 30,	"7" => 31
	);

	public static function generate_secret_key($length = 16) {
		$b32 	= "234567QWERTYUIOPASDFGHJKLZXCVBNM";
		$s 	= "";

		for ($i = 0; $i < $length; $i++)
			$s .= $b32[rand(0,31)];

		return $s;
	}

	public static function get_timestamp() {
		return floor(microtime(true)/self::keyRegeneration);
	}

	public static function base32_decode($b32) {

		$b32 	= strtoupper($b32);

		if (!preg_match('/^[ABCDEFGHIJKLMNOPQRSTUVWXYZ234567]+$/', $b32, $match))
			throw new Exception('Invalid characters in the base32 string.');

		$l 	= strlen($b32);
		$n	= 0;
		$j	= 0;
		$binary = "";

		for ($i = 0; $i < $l; $i++) {

			$n = $n << 5;
			$n = $n + self::$lut[$b32[$i]];
			$j = $j + 5;

			if ($j >= 8) {
				$j = $j - 8;
				$binary .= chr(($n & (0xFF << $j)) >> $j);
			}
		}

		return $binary;
	}

	public static function oath_hotp($key, $counter)
	{
	    if (strlen($key) < 8)
		throw new Exception('At least 16 base 32 characters');

	    $bin_counter = pack('N*', 0) . pack('N*', $counter);		// Counter must be 64-bit int
	    $hash 	 = hash_hmac ('sha1', $bin_counter, $key, true);

	    return str_pad(self::oath_truncate($hash), self::otpLength, '0', STR_PAD_LEFT);
	}

	public static function verify_key($b32seed, $key, $window = 4, $useTimeStamp = true) {

		$timeStamp = self::get_timestamp();

		if ($useTimeStamp !== true) $timeStamp = (int)$useTimeStamp;

		$binarySeed = self::base32_decode($b32seed);

		for ($ts = $timeStamp - $window; $ts <= $timeStamp + $window; $ts++)
			if (self::oath_hotp($binarySeed, $ts) == $key)
				return true;

		return false;

	}

	public static function oath_truncate($hash)
	{
	    $offset = ord($hash[19]) & 0xf;

	    return (
	        ((ord($hash[$offset+0]) & 0x7f) << 24 ) |
	        ((ord($hash[$offset+1]) & 0xff) << 16 ) |
	        ((ord($hash[$offset+2]) & 0xff) << 8 ) |
	        (ord($hash[$offset+3]) & 0xff)
	    ) % pow(10, self::otpLength);
	}

}

```

**`login.php.bak`**

```jsx
<?php

require('google2fa.php');
require('jsonhandler.php');

$FLAG = "";
if (isset($_ENV['FLAG'])) {
    $FLAG = $_ENV['FLAG'];
}

$USER_DB = [
    // Set the initial user
    "admin" => [
        "password_hash" => password_hash("admin", PASSWORD_DEFAULT),
        "key1" => Google2FA::generate_secret_key(),
        "key2" => Google2FA::generate_secret_key(),
        "key3" => Google2FA::generate_secret_key()
    ]
];

if (isset($_DATA['username'])) {
    
    if (!isset($USER_DB[$_DATA['username']])) {
        json_die('wrong username', 'username');
    }

    $user_data = $USER_DB[$_DATA['username']];

    if (!password_verify($_DATA['password'], $user_data['password_hash'])) {
        json_die('wrong password', 'password');
    }

    if (!Google2FA::verify_key($user_data['key1'], $_DATA['otp1'])) {
        json_die('wrong otp1', 'otp1');
    }
    if (!Google2FA::verify_key($user_data['key2'], $_DATA['otp2'])) {
        json_die('wrong otp2', 'otp2');
    }
    if (!Google2FA::verify_key($user_data['key3'], $_DATA['otp3'])) {
        json_die('wrong otp3', 'otp3');
    }

    json_response("Flag: " . $FLAG);
}

json_response("OK");
```

Analysis of the exposed `login.php.bak` file revealed that the application defines an initial hardcoded administrative account with the username `admin` and the plaintext password `admin`, which is hashed at runtime using `password_hash()`. This disclosure of default credentials significantly weakens the authentication mechanism and allows attackers to immediately attempt login using known values.

```jsx
$USER_DB = [
// Set the initial user
"admin" => [
"password_hash" => password_hash("admin", PASSWORD_DEFAULT),
"key1" => Google2FA::generate_secret_key(),
"key2" => Google2FA::generate_secret_key(),
"key3" => Google2FA::generate_secret_key()
]
];
```

`username : admin`

`password : admin`

![image.png](<../../.gitbook/assets/image (4) (2).png>)

As part of evaluating the authentication logic, I supplied arbitrary OTP values (e.g., `1234`) in all three MFA fields, solely to observe how the server validated the inputs. The request was intercepted using Burp Suite’s Proxy module and forwarded to the Repeater to analyze the backend verification behavior.

During testing, I replaced all OTP fields with the value `true` to observe how the backend handled non-numeric inputs. Due to PHP’s implicit type juggling, the string `true` is internally converted to the integer `0` when passed to the `Google2FA::verify_key()` function. The server then attempted to validate the OTP against a range of timestamps (window = 4), significantly increasing the likelihood of a false positive match. As a result, all three MFA checks were bypassed successfully, and the server returned the FLAG.

![image.png](<../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png>)

```jsx
Flag: ClOuDsEk_ReSeArCH_tEaM_CTF_2025{474a30a63ef1f14e252dc0922f811b16}
```
