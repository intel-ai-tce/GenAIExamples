# Visual Question and Answering

Visual Question Answering (VQA) is the task of answering open-ended questions based on an image. The input to models supporting this task is typically a combination of an image and a question, and the output is an answer expressed in natural language.

Some noteworthy use case examples for VQA include:

- Accessibility applications for visually impaired individuals.
- Education: posing questions about visual materials presented in lectures or textbooks. VQA can also be utilized in interactive museum exhibits or historical sites.
- Customer service and e-commerce: VQA can enhance user experience by letting users ask questions about products.
- Image retrieval: VQA models can be used to retrieve images with specific characteristics. For example, the user can ask “Is there a dog?” to find all images with dogs from a set of images.

General architecture of VQA shows below:

![VQA](./assets/img/vqa.png)

The VisualQnA example is implemented using the component-level microservices defined in [GenAIComps](https://github.com/opea-project/GenAIComps). The flow chart below shows the information flow between different microservices for this example.

```mermaid
---
config:
  flowchart:
    nodeSpacing: 400
    rankSpacing: 100
    curve: linear
  themeVariables:
    fontSize: 50px
---
flowchart LR
    %% Colors %%
    classDef blue fill:#ADD8E6,stroke:#ADD8E6,stroke-width:2px,fill-opacity:0.5
    classDef orange fill:#FBAA60,stroke:#ADD8E6,stroke-width:2px,fill-opacity:0.5
    classDef orchid fill:#C26DBC,stroke:#ADD8E6,stroke-width:2px,fill-opacity:0.5
    classDef invisible fill:transparent,stroke:transparent;
    style VisualQnA-MegaService stroke:#000000

    %% Subgraphs %%
    subgraph VisualQnA-MegaService["VisualQnA MegaService "]
        direction LR
        LVM([LVM MicroService]):::blue
    end
    subgraph UserInterface[" User Interface "]
        direction LR
        a([User Input Query]):::orchid
        Ingest([Ingest data]):::orchid
        UI([UI server<br>]):::orchid
    end


    LVM_gen{{LVM Service <br>}}
    GW([VisualQnA GateWay<br>]):::orange
    NG([Nginx MicroService]):::blue


    %% Questions interaction
    direction LR
    Ingest[Ingest data] --> UI
    a[User Input Query] --> |Need Proxy Server|NG
    a[User Input Query] --> UI
    NG --> UI
    UI --> GW
    GW <==> VisualQnA-MegaService


    %% Embedding service flow
    direction LR
    LVM <-.-> LVM_gen

```

This example guides you through how to deploy a [LLaVA-NeXT](https://github.com/LLaVA-VL/LLaVA-NeXT) (Open Large Multimodal Models) model on [Intel Gaudi2](https://www.intel.com/content/www/us/en/products/details/processors/ai-accelerators/gaudi-overview.html) and [Intel Xeon Scalable Processors](https://www.intel.com/content/www/us/en/products/details/processors/xeon.html). We invite contributions from other hardware vendors to expand the OPEA ecosystem.

![llava screenshot](./assets/img/llava_screenshot1.png)
![llava-screenshot](./assets/img/llava_screenshot2.png)

## Required Models

By default, the model is set to `llava-hf/llava-v1.6-mistral-7b-hf`. To use a different model, update the `LVM_MODEL_ID` variable in the [`set_env.sh`](./docker_compose/intel/hpu/gaudi/set_env.sh) file.

```
export LVM_MODEL_ID="llava-hf/llava-v1.6-mistral-7b-hf"
```

You can choose other llava-next models, such as `llava-hf/llava-v1.6-vicuna-13b-hf`, as needed.

## Deploy VisualQnA Service

The VisualQnA service can be effortlessly deployed on either Intel Gaudi2 or Intel Xeon Scalable Processors.

Currently we support deploying VisualQnA services with docker compose.

### Setup Environment Variable

To set up environment variables for deploying VisualQnA services, follow these steps:

1. Set the required environment variables:

   ```bash
   # Example: host_ip="192.168.1.1"
   export host_ip="External_Public_IP"
   # Example: no_proxy="localhost, 127.0.0.1, 192.168.1.1"
   export no_proxy="Your_No_Proxy"
   ```

2. If you are in a proxy environment, also set the proxy-related environment variables:

   ```bash
   export http_proxy="Your_HTTP_Proxy"
   export https_proxy="Your_HTTPs_Proxy"
   ```

3. Set up other environment variables:

   > Notice that you can only choose **one** command below to set up envs according to your hardware. Other that the port numbers may be set incorrectly.

   ```bash
   # on Gaudi
   source ./docker_compose/intel/hpu/gaudi/set_env.sh
   # on Xeon
   source ./docker_compose/intel/cpu/xeon/set_env.sh
   ```

### Deploy VisualQnA on Gaudi

Refer to the [Gaudi Guide](./docker_compose/intel/hpu/gaudi/README.md) to build docker images from source.

Find the corresponding [compose.yaml](./docker_compose/intel/hpu/gaudi/compose.yaml).

```bash
cd GenAIExamples/VisualQnA/docker_compose/intel/hpu/gaudi/
docker compose up -d
```

### Deploy VisualQnA on Xeon

Refer to the [Xeon Guide](./docker_compose/intel/cpu/xeon/README.md) for more instructions on building docker images from source.

Find the corresponding [compose.yaml](./docker_compose/intel/cpu/xeon/compose.yaml).

```bash
cd GenAIExamples/VisualQnA/docker_compose/intel/cpu/xeon/
docker compose up -d
```

### Deploy VisualQnA on Kubernetes using Helm Chart

Refer to the [VisualQnA helm chart](./kubernetes/helm/README.md) for instructions on deploying VisualQnA on Kubernetes.

## Validated Configurations

| **Deploy Method** | **LLM Engine** | **LLM Model**                     | **Hardware** |
| ----------------- | -------------- | --------------------------------- | ------------ |
| Docker Compose    | TGI, vLLM      | llava-hf/llava-v1.6-mistral-7b-hf | Intel Xeon   |
| Docker Compose    | TGI, vLLM      | llava-hf/llava-1.5-7b-hf          | Intel Gaudi  |
| Docker Compose    | TGI, vLLM      | Xkev/Llama-3.2V-11B-cot           | AMD ROCm     |
| Helm Charts       | TGI, vLLM      | llava-hf/llava-v1.6-mistral-7b-hf | Intel Gaudi  |
| Helm Charts       | TGI, vLLM      | llava-hf/llava-v1.6-mistral-7b-hf | Intel Xeon   |
