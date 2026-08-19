# AI Infrastructure Reconnaissance

### Introduction

Traditional security reconnaissance focuses on identifying:

* Web applications
* APIs
* Databases
* Authentication systems
* Cloud services

However, modern AI systems introduce entirely new infrastructure components that traditional security tools often fail to identify correctly.

AI Infrastructure Reconnaissance is the process of discovering, fingerprinting, enumerating, and mapping AI-related services, models, and supporting infrastructure.

The goal is not exploitation.

The goal is visibility.

Security teams cannot protect what they cannot see.

***

## Why AI Reconnaissance Matters

When organizations deploy AI capabilities, their attack surface expands significantly.

New services appear such as:

* Inference Servers
* Model Registries
* Experiment Tracking Platforms
* Vector Databases
* Notebook Servers
* Object Storage Systems

Most traditional vulnerability scanners were not designed to recognize these technologies.

As a result:

* Security teams may not know they exist.
* Misconfigurations remain unnoticed.
* Attackers gain an advantage.

***

## Traditional Infrastructure vs AI Infrastructure

### Traditional Environment

```
User 
 ↓
Web Application 
 ↓
API 
 ↓
Database
```

***

### AI-Augmented Environment

```
User 
  ↓
Application 
  ↓
LLM / Inference Server 
  ↓
Vector Database 
  ↓
Model Registry 
  ↓
Object Storage 
  ↓
Monitoring Stack
```

***

### Security Impact

Traditional attack surfaces:

* Web Servers
* Databases
* APIs

AI attack surfaces:

* Models
* Embeddings
* Vector Stores
* Model Registries
* Prompt Construction Layers
* Inference Endpoints

***

## Reconnaissance Methodology

A structured AI reconnaissance engagement consists of five phases.

```
Passive Reconnaissance
         ↓
Active Scanning
         ↓
Fingerprinting
         ↓
Enumeration
         ↓
Supply Chain Analysis
```

***

## Phase 1: Passive Reconnaissance

### Objective

Collect information without interacting directly with the target.

***

### Common Sources

#### Shodan

Search for exposed AI services.

Examples:

```
port:5000 "MLflow"
```

```
port:8888 "Jupyter"
```

```
http.title:"Ray Dashboard"
```

***

#### Censys

Identify exposed AI infrastructure.

***

#### GitHub

Search for:

* API keys
* Hugging Face tokens
* MLflow configurations
* Internal URLs

Example:

```
HF_TOKEN
```

```
MLFLOW_TRACKING_URI
```

***

### Goals

Identify:

* Public infrastructure
* Configuration leaks
* Credentials
* Supply chain dependencies

***

## Phase 2: Active Scanning

### Objective

Identify AI services running on the network.

***

### Nmap Scanning

Example:

```
nmap -sV -p 5000,6333,8000,8001,8265,8888,9000 target
```

***

### Common AI Ports

| Port  | Service       |
| ----- | ------------- |
| 5000  | MLflow        |
| 6333  | Qdrant        |
| 8000  | Triton        |
| 8001  | Triton gRPC   |
| 8265  | Ray Dashboard |
| 8888  | Jupyter       |
| 9000  | MinIO         |
| 11434 | Ollama        |

***

### Goal

Discover:

* AI services
* Open ports
* Accessible endpoints

***

## AI Infrastructure Components

***

## MLflow

### Purpose

Machine Learning lifecycle management platform.

Tracks:

* Experiments
* Models
* Metrics
* Parameters
* Artifacts

***

### Default Port

```
5000
```

***

### Security Risks

Exposed MLflow instances may reveal:

* Model names
* User IDs
* Experiment history
* Artifact locations

***

## Triton Inference Server

### Purpose

Serves machine learning models in production.

***

### Default Ports

```
8000
8001
8002
```

***

### Security Risks

Attackers may discover:

* Available models
* Input schemas
* Framework details

***

## Vector Databases

Examples:

* Qdrant
* Weaviate
* Milvus
* Chroma

***

### Purpose

Store embeddings for retrieval systems.

***

### Security Risks

Exposure may reveal:

* Internal knowledge bases
* Collection names
* Business data structures

***

## Jupyter Notebooks

### Purpose

Interactive environment for data scientists.

***

### Default Port

```
8888
```

***

### Security Risks

Common findings:

* Cloud credentials
* API keys
* Database passwords
* Hugging Face tokens
* MLflow credentials

***

## MinIO

### Purpose

Object storage system.

***

### Stores

* Models
* Datasets
* Artifacts

***

### Default Ports

```
9000
9001
```

***

### Security Risks

Exposure may lead to:

* Model theft
* Dataset leakage
* Intellectual property loss

***

## Ray

### Purpose

Distributed AI execution framework.

***

### Default Port

```
8265
```

***

### Security Risks

Misconfigured Ray deployments have been abused for:

* Remote code execution
* Credential theft
* Cryptocurrency mining

***

## Phase 3: Fingerprinting

### Objective

Identify exactly which AI technology is running.

Finding an open port is not enough.

We need to determine:

```
What service is this?
```

***

### Header Analysis

Example:

```
Server: TorchServe
```

Directly identifies the framework.

***

### JSON Analysis

Example:

```
{  "object": "model"}
```

Suggests an OpenAI-compatible API.

***

### Error Fingerprinting

Malformed requests often reveal:

* Framework names
* Stack traces
* Internal components

***

### Endpoint Discovery

Common AI endpoints:

```
/v1/models
/v2/models
/predict
/infer
/embeddings
```

***

## gRPC Fingerprinting

Many AI services expose gRPC interfaces.

***

### Tool

```
grpcurl
```

***

### Example

```
grpcurl -plaintext target:8001 list
```

***

### Reflection Output

Example:

```
inference.GRPCInferenceService
grpc.health.v1.Health
grpc.reflection.v1alpha.ServerReflection
```

***

### Importance

Reflection reveals:

* Services
* Methods
* Schemas

without documentation.

***

## Phase 4: Enumeration

### Objective

Extract valuable information from identified services.

Fingerprinting answers:

```
What is this?
```

Enumeration answers:

```
What can I learn from it?
```

***

## MLflow Enumeration

Retrieve:

* Models
* Experiments
* Versions
* Artifact locations

Important endpoint:

```
/api/2.0/mlflow/model-versions/search
```

***

### Information Exposed

* Model names
* Artifact URIs
* User IDs
* Production labels

***

## Triton Enumeration

Endpoint:

```
/v2/models/{model}/config
```

***

### Information Exposed

* Input schema
* Output schema
* Tensor formats
* Framework type

***

## Vector Database Enumeration

Retrieve:

* Collection names
* Embedding dimensions
* Point counts

***

### Example

```
internal-kb-embeddings
```

Immediately reveals:

```
Internal Knowledge Base
```

***

## Jupyter Enumeration

Enumerate:

* Notebook files
* Environment variables
* Embedded credentials

***

### Common Findings

```
MLFLOW_TRACKING_PASSWORD
```

```
HF_TOKEN
```

```
AWS_SECRET_ACCESS_KEY
```

***

## Attack Path Mapping

Reconnaissance findings become valuable when connected together.

***

### Example Attack Chain

```
Exposed Jupyter Notebook            
       ↓
MLflow Credentials            
       ↓
MLflow Access            
       ↓
Artifact Storage Discovery            
       ↓
Model Download            
       ↓
Model Theft
```

***

### Key Lesson

Individual findings often appear low risk.

Connected findings create serious attack paths.

***

## AI Supply Chain Analysis

Modern AI systems depend on:

* Pre-trained models
* Hugging Face repositories
* Open-source frameworks
* Third-party datasets

***

### Common Dependencies

Example:

```
sentence-transformers/all-MiniLM-L6-v2
```

***

### Risks

* Dependency confusion
* Malicious models
* Backdoored datasets
* Compromised repositories

***

## MITRE ATLAS Mapping

***

### AML.TA0002

#### Reconnaissance

Covers:

* Discovery
* Enumeration
* Fingerprinting

***

### AML.T0006

#### Active Scanning

Examples:

* Nmap
* Curl
* Service discovery

***

### AML.T0007

#### Discover ML Artifacts

Examples:

* Model registries
* Artifacts
* Experiment metadata

***

### AML.T0010

#### ML Supply Chain Compromise

Examples:

* Exposed Hugging Face tokens
* Poisoned dependencies
* Malicious models

***

### AML.T0014

#### Discover ML Model Family

Examples:

* GPT
* Llama
* Claude
* Mistral

***

## Detection Opportunities

Defenders should monitor:

***

### Model Enumeration

Repeated requests to:

```
/v2/models
```

***

### MLflow Enumeration

Requests to:

```
/registered-models/list
/model-versions/search
```

without corresponding UI activity.

***

### Jupyter Enumeration

Requests to:

```
/api/contents
/api/kernels
```

without authentication.

***

### AI Port Scanning

Sequential probes of:

```
5000
6333
8000
8001
8265
8888
9000
```

***

## Security Best Practices

### MLflow

* Enable authentication
* Restrict public access

***

### Jupyter

* Require authentication
* Disable anonymous access
* Restrict network exposure

***

### MinIO

* Disable public buckets
* Enforce access controls

***

### Hugging Face Tokens

* Use least privilege
* Rotate regularly
* Avoid storing in notebooks

***

### Monitoring

Track:

* Enumeration activity
* API abuse
* Unusual model access
* Supply chain changes

***

## Key Takeaways

1. AI infrastructure introduces entirely new attack surfaces.
2. Traditional scanners often fail to identify AI-specific technologies.
3. Reconnaissance follows five phases:
   * Passive Recon
   * Active Scanning
   * Fingerprinting
   * Enumeration
   * Supply Chain Analysis
4. MLflow, Triton, Jupyter, Ray, MinIO, and Vector Databases are high-value targets.
5. Attackers rarely stop at discovery; they connect findings into attack paths.
6. MITRE ATLAS provides a framework for AI reconnaissance activities.
7. Most AI breaches begin with visibility failures rather than advanced exploitation.
8. Effective monitoring can detect reconnaissance long before exploitation occurs.
