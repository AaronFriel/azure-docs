---
title: How to deploy Meta Llama models with Azure Machine Learning studio
titleSuffix: Azure Machine Learning
description: Learn how to deploy Meta Llama models with Azure Machine Learning studio.
manager: scottpolly
ms.service: machine-learning
ms.subservice: inferencing
ms.topic: how-to
ms.date: 04/16/2024
ms.reviewer: shubhiraj
reviewer: shubhirajMsft
ms.author: ssalgado
author: ssalgadodev
ms.custom: references_regions, build-2024

#This functionality is also available in Azure AI Studio: /azure/ai-studio/how-to/deploy-models-llama.md
---


# How to deploy Meta Llama models with Azure Machine Learning studio

In this article, you learn about the Meta Llama models (LLMs). You also learn how to use Azure Machine Learning studio to deploy models from this set either to serverless APIs with pay-as you go billing or to managed compute.

> [!IMPORTANT]
> Read more about the announcement of Meta Llama 3 models available now on Azure AI Model Catalog: [Microsoft Tech Community Blog](https://aka.ms/Llama3Announcement) and from [Meta Announcement Blog](https://aka.ms/meta-llama3-announcement-blog).

Meta Llama 3 models and tools are a collection of pretrained and fine-tuned generative text models ranging in scale from 8 billion to 70 billion parameters. The Meta Llama model family also includes fine-tuned versions optimized for dialogue use cases with reinforcement learning from human feedback (RLHF), called Meta-Llama-3-8B-Instruct and Meta-Llama-3-70B-Instruct. See the following GitHub samples to explore integrations with [LangChain](https://aka.ms/meta-llama3-langchain-sample), [LiteLLM](https://aka.ms/meta-llama3-litellm-sample), [OpenAI](https://aka.ms/meta-llama3-openai-sample) and the [Azure API](https://aka.ms/meta-llama3-azure-api-sample).

[!INCLUDE [machine-learning-preview-generic-disclaimer](includes/machine-learning-preview-generic-disclaimer.md)]

## Deploy Meta Llama models as a serverless API

Certain models in the model catalog can be deployed as a serverless API with pay-as-you-go billing, providing a way to consume them as an API without hosting them on your subscription while keeping the enterprise security and compliance organizations need. This deployment option doesn't require quota from your subscription.

Meta Llama models are deployed as a serverless API with pay-as-you-go billing are offered by Meta AI through Microsoft Azure Marketplace, and they might add more terms of use and pricing.

### Azure Marketplace model offerings

The following models are available in Azure Marketplace for Meta Llama models when deployed as a serverless API with pay-as-you-go billing:

# [Meta Llama 3](#tab/llama-three)

* [Meta Llama-3-8B (preview)](https://aka.ms/aistudio/landing/meta-llama-3-8b-base)
* [Meta Llama-3-70B (preview)](https://aka.ms/aistudio/landing/meta-llama-3-70b-base)

If you need to deploy a different model, [deploy it to managed compute](#deploy-meta-llama-models-to-managed-compute) instead.

# [Meta Llama 2](#tab/llama-two)

* Meta Llama-2-7B (preview)
* Meta Llama 2 7B-Chat (preview)
* Meta Llama-2-13B (preview)
* Meta Llama 2 13B-Chat (preview)
* Meta Llama-2-70B (preview)
* Meta Llama 2 70B-Chat (preview)

If you need to deploy a different model, [deploy it to managed compute](#deploy-meta-llama-models-to-managed-compute) instead.

---

### Prerequisites

# [Meta Llama 3](#tab/llama-three)

- An Azure subscription with a valid payment method. Free or trial Azure subscriptions won't work. If you don't have an Azure subscription, create a [paid Azure account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) to begin.
- An Azure Machine Learning workspace and a compute instance. If you don't have these, use the steps in the [Quickstart: Create workspace resources](quickstart-create-resources.md) article to create them. The serverless API model deployment offering for Meta Llama 3 is only available with workspaces created in these regions:

     * East US
     * East US 2
     * North Central US
     * South Central US
     * West US
     * West US 3
     * Sweden Central
    
    For a list of  regions that are available for each of the models supporting serverless API endpoint deployments, see [Region availability for models in serverless API endpoints](concept-endpoint-serverless-availability.md).

- Azure role-based access controls (Azure RBAC) are used to grant access to operations in Azure Machine Learning. To perform the steps in this article, your user account must be assigned the __owner__ or __contributor__ role for the Azure subscription. Alternatively, your account can be assigned a custom role that has the following permissions:

    - On the Azure subscription—to subscribe the workspace to the Azure Marketplace offering, once for each workspace, per offering:
      - `Microsoft.MarketplaceOrdering/agreements/offers/plans/read`
      - `Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action`
      - `Microsoft.MarketplaceOrdering/offerTypes/publishers/offers/plans/agreements/read`
      - `Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read`
      - `Microsoft.SaaS/register/action`
 
    - On the resource group—to create and use the SaaS resource:
      - `Microsoft.SaaS/resources/read`
      - `Microsoft.SaaS/resources/write`
 
    - On the workspace—to deploy endpoints (the Azure Machine Learning data scientist role contains these permissions already):
      - `Microsoft.MachineLearningServices/workspaces/marketplaceModelSubscriptions/*`  
      - `Microsoft.MachineLearningServices/workspaces/serverlessEndpoints/*`

    For more information on permissions, see [Manage access to an Azure Machine Learning workspace](how-to-assign-roles.md).


# [Meta Llama 2](#tab/llama-two)

- An Azure subscription with a valid payment method. Free or trial Azure subscriptions won't work. If you don't have an Azure subscription, create a [paid Azure account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) to begin.
- An Azure Machine Learning workspace and a compute instance. If you don't have these, use the steps in the [Quickstart: Create workspace resources](quickstart-create-resources.md) article to create them. The serverless API model deployment offering for Meta Llama 2 is only available with workspaces created in these regions:

     * East US
     * East US 2
     * North Central US
     * South Central US
     * West US
     * West US 3
    
    For a list of  regions that are available for each of the models supporting serverless API endpoint deployments, see [Region availability for models in serverless API endpoints](concept-endpoint-serverless-availability.md).

- Azure role-based access controls (Azure RBAC) are used to grant access to operations in Azure Machine Learning. To perform the steps in this article, your user account must be assigned the __owner__ or __contributor__ role for the Azure subscription. Alternatively, your account can be assigned a custom role that has the following permissions:

    - On the Azure subscription—to subscribe the workspace to the Azure Marketplace offering, once for each workspace, per offering:
      - `Microsoft.MarketplaceOrdering/agreements/offers/plans/read`
      - `Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action`
      - `Microsoft.MarketplaceOrdering/offerTypes/publishers/offers/plans/agreements/read`
      - `Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read`
      - `Microsoft.SaaS/register/action`
 
    - On the resource group—to create and use the SaaS resource:
      - `Microsoft.SaaS/resources/read`
      - `Microsoft.SaaS/resources/write`
 
    - On the workspace—to deploy endpoints (the Azure Machine Learning data scientist role contains these permissions already):
      - `Microsoft.MachineLearningServices/workspaces/marketplaceModelSubscriptions/*`  
      - `Microsoft.MachineLearningServices/workspaces/serverlessEndpoints/*`

    For more information on permissions, see [Manage access to an Azure Machine Learning workspace](how-to-assign-roles.md).
  
---

### Create a new deployment

To create a deployment:

# [Meta Llama 3](#tab/llama-three)

1. Go to [Azure Machine Learning studio](https://ml.azure.com/home).
1. Select the workspace in which you want to deploy your models. To use the pay-as-you-go model deployment offering, your workspace must belong to the **East US 2** or **Sweden Central** region.
1. Choose the model you want to deploy from the [model catalog](https://ml.azure.com/model/catalog).

   Alternatively, you can initiate deployment by going to your workspace and selecting **Endpoints** > **Serverless endpoints** > **Create**.

1. On the model's overview page, select **Deploy** and then **Serverless API with Azure AI Content Safety**.

1. On the deployment wizard, select the link to **Azure Marketplace Terms** to learn more about the terms of use. You can also select the **Marketplace offer details** tab to learn about pricing for the selected model.
1. If this is your first time deploying the model in the workspace, you have to subscribe your workspace for the particular offering (for example, Meta-Llama-3-70B) from Azure Marketplace. This step requires that your account has the Azure subscription permissions and resource group permissions listed in the prerequisites. Each workspace has its own subscription to the particular Azure Marketplace offering, which allows you to control and monitor spending. Select **Subscribe and Deploy**.

    > [!NOTE]
    > Subscribing a workspace to a particular Azure Marketplace offering (in this case, Llama-3-70B) requires that your account has **Contributor** or **Owner** access at the subscription level where the project is created. Alternatively, your user account can be assigned a custom role that has the Azure subscription permissions and resource group permissions listed in the [prerequisites](#prerequisites).

1. Once you sign up the workspace for the particular Azure Marketplace offering, subsequent deployments of the _same_ offering in the _same_ workspace don't require subscribing again. Therefore, you don't need to have the subscription-level permissions for subsequent deployments. If this scenario applies to you, select **Continue to deploy**.

1. Give the deployment a name. This name becomes part of the deployment API URL. This URL must be unique in each Azure region.

1. Select **Deploy**. Wait until the deployment is finished and you're redirected to the serverless endpoints page.
1. Select the endpoint to open its Details page.
1. Select the **Test** tab to start interacting with the model.
1. You can also take note of the **Target** URL and the **Secret Key** to call the deployment and generate completions.   
1. You can always find the endpoint's details, URL, and access keys by navigating to **Workspace** > **Endpoints** > **Serverless endpoints**.

# [Meta Llama 2](#tab/llama-two)

1. Go to [Azure Machine Learning studio](https://ml.azure.com/home).
1. Select the workspace in which you want to deploy your models. To use the pay-as-you-go model deployment offering, your workspace must belong to the **East US 2** or **West US 3** region.
1. Choose the model you want to deploy from the [model catalog](https://ml.azure.com/model/catalog).

   Alternatively, you can initiate deployment by going to your workspace and selecting **Endpoints** > **Serverless endpoints** > **Create**.

1. On the model's overview page, select **Deploy** and then **Serverless API with Azure AI Content Safety**.

    :::image type="content" source="media/how-to-deploy-models-llama/deploy-pay-as-you-go.png" alt-text="A screenshot showing how to deploy a model with the pay-as-you-go option." lightbox="media/how-to-deploy-models-llama/deploy-pay-as-you-go.png":::

1. On the deployment wizard, select the link to **Azure Marketplace Terms** to learn more about the terms of use. You can also select the **Marketplace offer details** tab to learn about pricing for the selected model.
1. If this is your first time deploying the model in the workspace, you have to subscribe your workspace for the particular offering (for example, Llama-2-70b) from Azure Marketplace. This step requires that your account has the Azure subscription permissions and resource group permissions listed in the prerequisites. Each workspace has its own subscription to the particular Azure Marketplace offering, which allows you to control and monitor spending. Select **Subscribe and Deploy**.

    > [!NOTE]
    > Subscribing a workspace to a particular Azure Marketplace offering (in this case, Llama-2-70b) requires that your account has **Contributor** or **Owner** access at the subscription level where the project is created. Alternatively, your user account can be assigned a custom role that has the Azure subscription permissions and resource group permissions listed in the [prerequisites](#prerequisites).

    :::image type="content" source="media/how-to-deploy-models-llama/deploy-marketplace-terms.png" alt-text="A screenshot showing the terms and conditions of a given model." lightbox="media/how-to-deploy-models-llama/deploy-marketplace-terms.png":::

1. Once you sign up the workspace for the particular Azure Marketplace offering, subsequent deployments of the _same_ offering in the _same_ workspace don't require subscribing again. Therefore, you don't need to have the subscription-level permissions for subsequent deployments. If this scenario applies to you, select **Continue to deploy**.

    :::image type="content" source="media/how-to-deploy-models-llama/deploy-pay-as-you-go-project.png" alt-text="A screenshot showing a project that is already subscribed to the offering." lightbox="media/how-to-deploy-models-llama/deploy-pay-as-you-go-project.png":::

1. Give the deployment a name. This name becomes part of the deployment API URL. This URL must be unique in each Azure region.

    :::image type="content" source="media/how-to-deploy-models-llama/deployment-name.png" alt-text="A screenshot showing how to indicate the name of the deployment you want to create." lightbox="media/how-to-deploy-models-llama/deployment-name.png":::

1. Select **Deploy**. Wait until the deployment is finished and you're redirected to the serverless endpoints page.
1. Select the endpoint to open its Details page.
1. Select the **Test** tab to start interacting with the model.
1. You can also take note of the **Target** URL and the **Secret Key** to call the deployment and generate completions.   
1. You can always find the endpoint's details, URL, and access keys by navigating to **Workspace** > **Endpoints** > **Serverless endpoints**.

---

To learn about billing for Meta Llama models deployed as a serverless API, see [Cost and quota considerations for Meta Llama models deployed as a serverless API](#cost-and-quota-considerations-for-meta-llama-models-deployed-as-a-serverless-api).

### Consume Meta Llama models as a service

Models deployed as a service can be consumed using either the chat or the completions API, depending on the type of model you deployed.

# [Meta Llama 3](#tab/llama-three)

1. In the **workspace**, select **Endpoints** > **Serverless endpoints**.
1. Find and select the deployment you created.
1. Copy the **Target** URL and the **Key** token values.
1. Make an API request based on the type of model you deployed. 

    - For completions models, such as `Llama-3-8B`, use the [`<target_url>/v1/completions`](#completions-api) API.
    - For chat models, such as `Llama-3-8B-Instruct`, use the [`<target_url>/v1/chat/completions`](#chat-api) API.

   For more information on using the APIs, see the [reference](#reference-for-meta-llama-models-deployed-a-serverless-api) section.

# [Meta Llama 2](#tab/llama-two)

1. In the **workspace**, select **Endpoints** > **Serverless endpoints**.
1. Find and select the deployment you created.
1. Copy the **Target** URL and the **Key** token values.
1. Make an API request based on the type of model you deployed. 

    - For completions models, such as `Meta-Llama-2-7B`, use the [`/v1/completions`](#completions-api) API or the [Azure AI Model Inference API](reference-model-inference-api.md) on the route `/completions`.
    - For chat models, such as `Meta-Llama-2-7B-Chat`, use the [`/v1/chat/completions`](#chat-api) API or the [Azure AI Model Inference API](reference-model-inference-api.md) on the route `/chat/completions`.

    For more information on using the APIs, see the [reference](#reference-for-meta-llama-models-deployed-a-serverless-api) section.

---

### Reference for Meta Llama models deployed a serverless API

Llama models accept both the [Azure AI Model Inference API](reference-model-inference-api.md) on the route `/chat/completions` or a [Llama Chat API](#chat-api) on `/v1/chat/completions`. In the same way, text completions can be generated using the [Azure AI Model Inference API](reference-model-inference-api.md) on the route `/completions` or a [Llama Completions API](#completions-api) on `/v1/completions`

The [Azure AI Model Inference API](reference-model-inference-api.md) schema can be found in the [reference for Chat Completions](reference-model-inference-chat-completions.md) article and an [OpenAPI specification can be obtained from the endpoint itself](reference-model-inference-api.md?tabs=rest#getting-started).

#### Completions API

Use the method `POST` to send the request to the `/v1/completions` route:

__Request__

```rest
POST /v1/completions HTTP/1.1
Host: <DEPLOYMENT_URI>
Authorization: Bearer <TOKEN>
Content-type: application/json
```

#### Request schema

Payload is a JSON formatted string containing the following parameters:

| Key           | Type      | Default | Description    |
|---------------|-----------|---------|----------------|
| `prompt`      | `string`  |  No default. This value must be specified.  | The prompt to send to the model. |
| `stream`      | `boolean` | `False`  | Streaming allows the generated tokens to be sent as data-only server-sent events whenever they become available. |
| `max_tokens`  | `integer` | `16`    | The maximum number of tokens to generate in the completion. The token count of your prompt plus `max_tokens` can't exceed the model's context length.  |
| `top_p`       | `float`   | `1`     | An alternative to sampling with temperature, called nucleus sampling, where the model considers the results of the tokens with `top_p` probability mass. So 0.1 means only the tokens comprising the top 10% probability mass are considered. We generally recommend altering `top_p` or `temperature`, but not both. |
| `temperature` | `float`   | `1`     | The sampling temperature to use, between 0 and 2. Higher values mean the model samples more broadly the distribution of tokens. Zero means greedy sampling. We recommend altering this or `top_p`, but not both. |
| `n`           | `integer` | `1`     | How many completions to generate for each prompt. <br>Note: Because this parameter generates many completions, it can quickly consume your token quota. |
| `stop`        | `array`   | `null`  | String or a list of strings containing the word where the API stops generating further tokens. The returned text won't contain the stop sequence.   |
| `best_of`     | `integer` | `1`     | Generates `best_of` completions server-side and returns the "best" (the one with the lowest log probability per token). Results can't be streamed. When used with `n`, `best_of` controls the number of candidate completions and `n` specifies how many to return—best_of must be greater than `n`. <br>Note: Because this parameter generates many completions, it can quickly consume your token quota.|
| `logprobs` | `integer` |  `null` | A number indicating to include the log probabilities on the `logprobs` most likely tokens and the chosen tokens. For example, if `logprobs` is 10, the API returns a list of the 10 most likely tokens. the API always returns the logprob of the sampled token, so there might be up to `logprobs`+1 elements in the response.  |
| `presence_penalty`    | `float`   | `null`  | Number between -2.0 and 2.0. Positive values penalize new tokens based on whether they appear in the text so far, increasing the model's likelihood to talk about new topics. |
| `ignore_eos`          | `boolean` | `True`  | Whether to ignore the EOS token and continue generating tokens after the EOS token is generated.  |
| `use_beam_search`     | `boolean` | `False` | Whether to use beam search instead of sampling. In such case, `best_of` must be greater than `1` and `temperature` must be `0`. |
| `stop_token_ids`      | `array`   | `null`  | List of IDs for tokens that, when generated, stop further token generation. The returned output contains the stop tokens unless the stop tokens are special tokens. |
| `skip_special_tokens` | `boolean` | `null`  | Whether to skip special tokens in the output. |

#### Example

__Body__

```json
{
    "prompt": "What's the distance to the moon?",
    "temperature": 0.8,
    "max_tokens": 512,
}
```

#### Response schema

The response payload is a dictionary with the following fields.

| Key       | Type      | Description                                                              |
|-----------|-----------|--------------------------------------------------------------------------|
| `id`      | `string`  | A unique identifier for the completion.                                  |
| `choices` | `array`   | The list of completion choices the model generated for the input prompt. |
| `created` | `integer` | The Unix timestamp (in seconds) of when the completion was created.      |
| `model`   | `string`  | The model_id used for completion.                                        |
| `object`  | `string`  | The object type, which is always `text_completion`.                      |
| `usage`   | `object`  | Usage statistics for the completion request.                             |

> [!TIP]
> In the streaming mode, for each chunk of response, `finish_reason` is always `null`, except from the last one which is terminated by a payload `[DONE]`. 


The `choices` object is a dictionary with the following fields. 

| Key     | Type      | Description  |
|---------|-----------|------|
| `index` | `integer` | Choice index. When `best_of` > 1, the index in this array might not be in order and might not be 0 to n-1. |
| `text`  | `string`  | Completion result. |
| `finish_reason` | `string` | The reason the model stopped generating tokens: <br>- `stop`: model hit a natural stop point, or a provided stop sequence. <br>- `length`: if max number of tokens have been reached. <br>- `content_filter`: When RAI moderates and CMP forces moderation. <br>- `content_filter_error`: an error during moderation and wasn't able to make decision on the response. <br>- `null`: API response still in progress or incomplete. |
| `logprobs` | `object` | The log probabilities of the generated tokens in the output text. |

The `usage` object is a dictionary with the following fields. 

| Key                 | Type      | Value                                         |
|---------------------|-----------|-----------------------------------------------|
| `prompt_tokens`     | `integer` | Number of tokens in the prompt.               |
| `completion_tokens` | `integer` | Number of tokens generated in the completion. |
| `total_tokens`      | `integer` | Total tokens.                                 |

The `logprobs` object is a dictionary with the following fields:

| Key              | Type  | Value |
|------------------|-------------------------|----|
| `text_offsets`   | `array` of `integers`   | The position or index of each token in the completion output.  |
| `token_logprobs` | `array` of `float`      | Selected `logprobs` from dictionary in `top_logprobs` array.  |
| `tokens`         | `array` of `string`     | Selected tokens. |
| `top_logprobs`   | `array` of `dictionary` | Array of dictionary. In each dictionary, the key is the token and the value is the prob. |

#### Example

```json
{
    "id": "12345678-1234-1234-1234-abcdefghijkl",
    "object": "text_completion",
    "created": 217877,
    "choices": [
        {
            "index": 0,
            "text": "The Moon is an average of 238,855 miles away from Earth, which is about 30 Earths away.",
            "logprobs": null,
            "finish_reason": "stop"
        }
    ],
    "usage": {
        "prompt_tokens": 7,
        "total_tokens": 23,
        "completion_tokens": 16
    }
}
```

#### Chat API

Use the method `POST` to send the request to the `/v1/chat/completions` route:

__Request__

```rest
POST /v1/chat/completions HTTP/1.1
Host: <DEPLOYMENT_URI>
Authorization: Bearer <TOKEN>
Content-type: application/json
```

#### Request schema

Payload is a JSON formatted string containing the following parameters:

| Key | Type | Default | Description |
|-----|-----|-----|-----|
| `messages`    | `string`  | No default. This value must be specified.  | The message or history of messages to use to prompt the model.  |
| `stream`      | `boolean` | `False` | Streaming allows the generated tokens to be sent as data-only server-sent events whenever they become available.  |
| `max_tokens`  | `integer` | `16`    | The maximum number of tokens to generate in the completion. The token count of your prompt plus `max_tokens` can't exceed the model's context length. |
| `top_p`       | `float`   | `1`     | An alternative to sampling with temperature, called nucleus sampling, where the model considers the results of the tokens with `top_p` probability mass. So 0.1 means only the tokens comprising the top 10% probability mass are considered. We generally recommend altering `top_p` or `temperature`, but not both.  |
| `temperature` | `float`   | `1`     | The sampling temperature to use, between 0 and 2. Higher values mean the model samples more broadly the distribution of tokens. Zero means greedy sampling. We recommend altering this or `top_p`, but not both.  |
| `n`           | `integer` | `1`     | How many completions to generate for each prompt. <br>Note: Because this parameter generates many completions, it can quickly consume your token quota. |
| `stop`        | `array`   | `null`  | String or a list of strings containing the word where the API stops generating further tokens. The returned text won't contain the stop sequence. |
| `best_of`     | `integer` | `1`     | Generates `best_of` completions server-side and returns the "best" (the one with the lowest log probability per token). Results can't be streamed. When used with `n`, `best_of` controls the number of candidate completions and `n` specifies how many to return—`best_of` must be greater than `n`. <br>Note: Because this parameter generates many completions, it can quickly consume your token quota.|
| `logprobs` | `integer` |  `null` | A number indicating to include the log probabilities on the `logprobs` most likely tokens and the chosen tokens. For example, if `logprobs` is 10, the API returns a list of the 10 most likely tokens. the API will always return the logprob of the sampled token, so there might be up to `logprobs`+1 elements in the response.  |
| `presence_penalty`    | `float`   | `null`  | Number between -2.0 and 2.0. Positive values penalize new tokens based on whether they appear in the text so far, increasing the model's likelihood to talk about new topics. |
| `ignore_eos`          | `boolean` | `True`  | Whether to ignore the EOS token and continue generating tokens after the EOS token is generated. |
| `use_beam_search`     | `boolean` | `False` | Whether to use beam search instead of sampling. In such case, `best_of` must be greater than `1` and `temperature` must be `0`. |
| `stop_token_ids`      | `array`   | `null`  | List of IDs for tokens that, when generated, stop further token generation. The returned output contains the stop tokens unless the stop tokens are special tokens.|
| `skip_special_tokens` | `boolean` | `null`  | Whether to skip special tokens in the output. |

The `messages` object has the following fields:

| Key       | Type      | Value |
|-----------|-----------|------------|
| `content` | `string` | The contents of the message. Content is required for all messages. |
| `role`    | `string` | The role of the message's author. One of `system`, `user`, or `assistant`. |


#### Example

__Body__

```json
{
    "messages":
    [
        { 
        "role": "system", 
        "content": "You are a helpful assistant that translates English to Italian."},
        {
        "role": "user", 
        "content": "Translate the following sentence from English to Italian: I love programming."
        }
    ],
    "temperature": 0.8,
    "max_tokens": 512,
}
```

#### Response schema

The response payload is a dictionary with the following fields.

| Key       | Type      | Description                                                                |
|-----------|-----------|----------------------------------------------------------------------------|
| `id`      | `string`  | A unique identifier for the completion.                                    |
| `choices` | `array`   | The list of completion choices the model generated for the input messages. |
| `created` | `integer` | The Unix timestamp (in seconds) of when the completion was created.        |
| `model`   | `string`  | The model_id used for completion.                                          |
| `object`  | `string`  | The object type, which is always `chat.completion`.                        |
| `usage`   | `object`  | Usage statistics for the completion request.                               |

> [!TIP]
> In the streaming mode, for each chunk of response, `finish_reason` is always `null`, except from the last one which is terminated by a payload `[DONE]`. In each `choices` object, the key for `messages` is changed by `delta`. 


The `choices` object is a dictionary with the following fields. 

| Key     | Type      | Description  |
|---------|-----------|--------------|
| `index` | `integer` | Choice index. When `best_of` > 1, the index in this array might not be in order and might not be `0` to `n-1`. |
| `messages` or `delta`   | `string`  | Chat completion result in `messages` object. When streaming mode is used, `delta` key is used.  |
| `finish_reason` | `string` | The reason the model stopped generating tokens: <br>- `stop`: model hit a natural stop point or a provided stop sequence. <br>- `length`: if max number of tokens have been reached. <br>- `content_filter`: When RAI moderates and CMP forces moderation <br>- `content_filter_error`: an error during moderation and wasn't able to make decision on the response <br>- `null`: API response still in progress or incomplete. |
| `logprobs` | `object` | The log probabilities of the generated tokens in the output text. |


The `usage` object is a dictionary with the following fields.

| Key                 | Type      | Value                                         |
|---------------------|-----------|-----------------------------------------------|
| `prompt_tokens`     | `integer` | Number of tokens in the prompt.               |
| `completion_tokens` | `integer` | Number of tokens generated in the completion. |
| `total_tokens`      | `integer` | Total tokens.                                 |

The `logprobs` object is a dictionary with the following fields:

| Key              | Type                    | Value   |
|------------------|-------------------------|---------|
| `text_offsets`   | `array` of `integers`   | The position or index of each token in the completion output. |
| `token_logprobs` | `array` of `float`      | Selected `logprobs` from dictionary in `top_logprobs` array.   |
| `tokens`         | `array` of `string`     | Selected tokens.   |
| `top_logprobs`   | `array` of `dictionary` | Array of dictionary. In each dictionary, the key is the token and the value is the prob. |

#### Example

The following is an example response:

```json
{
    "id": "12345678-1234-1234-1234-abcdefghijkl",
    "object": "chat.completion",
    "created": 2012359,
    "model": "",
    "choices": [
        {
            "index": 0,
            "finish_reason": "stop",
            "message": {
                "role": "assistant",
                "content": "Sure, I\'d be happy to help! The translation of ""I love programming"" from English to Italian is:\n\n""Amo la programmazione.""\n\nHere\'s a breakdown of the translation:\n\n* ""I love"" in English becomes ""Amo"" in Italian.\n* ""programming"" in English becomes ""la programmazione"" in Italian.\n\nI hope that helps! Let me know if you have any other sentences you\'d like me to translate."
            }
        }
    ],
    "usage": {
        "prompt_tokens": 10,
        "total_tokens": 40,
        "completion_tokens": 30
    }
}
```

## Deploy Meta Llama models to managed compute

Apart from deploying with the pay-as-you-go managed service, you can also deploy Llama 3 models to managed compute in Azure Machine Learning studio. When deployed to managed compute, you can select all the details about the infrastructure running the model, including the virtual machines to use and the number of instances to handle the load you're expecting. Models deployed to managed compute consume quota from your subscription. All the models in the Meta Llama family can be deployed to managed compute.

### Create a new deployment

# [Meta Llama 3](#tab/llama-three)

Follow these steps to deploy a model such as `Llama-3-7B-Instruct` to a real-time endpoint in [Azure Machine Learning studio](https://ml.azure.com).

1. Select the workspace in which you want to deploy the model.
1. Choose the model that you want to deploy from the studio's [model catalog](https://ml.azure.com/model/catalog).

   Alternatively, you can initiate deployment by going to your workspace and selecting **Endpoints** > **real-time endpoints** > **Create**.

1. On the model's overview page, select **Deploy** and then **Managed Compute without Azure AI Content Safety**.

1. On the **Deploy with Azure AI Content Safety (preview)** page, select **Skip Azure AI Content Safety** so that you can continue to deploy the model using the UI.

    > [!TIP]
    > In general, we recommend that you select **Enable Azure AI Content Safety (Recommended)** for deployment of the Meta Llama model. This deployment option is currently only supported using the Python SDK and it happens in a notebook.

1. Select **Proceed**.

    > [!TIP]
    > If you don't have enough quota available in the selected project, you can use the option **I want to use shared quota and I acknowledge that this endpoint will be deleted in 168 hours**.

1. Select the **Virtual machine** and the **Instance count** that you want to assign to the deployment.
1. Select if you want to create this deployment as part of a new endpoint or an existing one. Endpoints can host multiple deployments while keeping resource configuration exclusive for each of them. Deployments under the same endpoint share the endpoint URI and its access keys.
1. Indicate if you want to enable **Inferencing data collection (preview)**.
1. Indicate if you want to enable **Package Model (preview)**.
1. Select **Deploy**. After a few moments, the endpoint's **Details** page opens up.
1. Wait for the endpoint creation and deployment to finish. This step can take a few minutes.
1. Select the endpoint's **Consume** page to obtain code samples that you can use to consume the deployed model in your application.

For more information on how to deploy models to managed compute using the studio, see [Deploying foundation models to endpoints for inferencing](how-to-use-foundation-models.md#deploying-foundation-models-to-endpoints-for-inferencing).

# [Meta Llama 2](#tab/llama-two)

Follow these steps to deploy a model such as `Llama-2-7b-chat` to a real-time endpoint in [Azure Machine Learning studio](https://ml.azure.com).

1. Select the workspace in which you want to deploy the model.
1. Choose the model that you want to deploy from the studio's [model catalog](https://ml.azure.com/model/catalog).

   Alternatively, you can initiate deployment by going to your workspace and selecting **Endpoints** > **real-time endpoints** > **Create**.

1. On the model's overview page, select **Deploy** and then **Managed Compute without Azure AI Content Safety**.

    :::image type="content" source="media/how-to-deploy-models-llama/deploy-real-time-endpoint.png" alt-text="A screenshot showing how to deploy a model with the real-time endpoint option." lightbox="media/how-to-deploy-models-llama/deploy-real-time-endpoint.png":::

1. On the **Deploy with Azure AI Content Safety (preview)** page, select **Skip Azure AI Content Safety** so that you can continue to deploy the model using the UI.

    > [!TIP]
    > In general, we recommend that you select **Enable Azure AI Content Safety (Recommended)** for deployment of the Meta Llama model. This deployment option is currently only supported using the Python SDK and it happens in a notebook.

1. Select **Proceed**.

    > [!TIP]
    > If you don't have enough quota available in the selected project, you can use the option **I want to use shared quota and I acknowledge that this endpoint will be deleted in 168 hours**.

1. Select the **Virtual machine** and the **Instance count** that you want to assign to the deployment.
1. Select if you want to create this deployment as part of a new endpoint or an existing one. Endpoints can host multiple deployments while keeping resource configuration exclusive for each of them. Deployments under the same endpoint share the endpoint URI and its access keys.
1. Indicate if you want to enable **Inferencing data collection (preview)**.
1. Indicate if you want to enable **Package Model (preview)**.
1. Select **Deploy**. After a few moments, the endpoint's **Details** page opens up.
1. Wait for the endpoint creation and deployment to finish. This step can take a few minutes.
1. Select the endpoint's **Consume** page to obtain code samples that you can use to consume the deployed model in your application.

For more information on how to deploy models to managed compute using the studio, see [Deploying foundation models to endpoints for inferencing](how-to-use-foundation-models.md#deploying-foundation-models-to-endpoints-for-inferencing).

---

### Consume Meta Llama models deployed to managed compute

For reference about how to invoke Meta Llama 3 models deployed to real-time endpoints, see the model's card in Azure Machine Learning studio [model catalog](concept-model-catalog.md). Each model's card has an overview page that includes a description of the model, samples for code-based inferencing, fine-tuning, and model evaluation.

#### Additional inference examples

# [Meta Llama 3](#tab/llama-three)

| **Package**       | **Sample Notebook**                             |
|----------------|----------------------------------------|
| OpenAI SDK (experimental)    | [openaisdk.ipynb](https://github.com/Azure/azureml-examples/blob/main/sdk/python/foundation-models/meta-llama3/openaisdk.ipynb)                     |
| LangChain      | [langchain.ipynb](https://github.com/Azure/azureml-examples/blob/main/sdk/python/foundation-models/meta-llama3/langchain.ipynb)                                |
| WebRequests     | [webrequests.ipynb](https://github.com/Azure/azureml-examples/blob/main/sdk/python/foundation-models/meta-llama3/webrequests.ipynb)                           |
| LiteLLM SDK    | [litellm.ipynb](https://github.com/Azure/azureml-examples/blob/main/sdk/python/foundation-models/meta-llama3/litellm.ipynb)         |

# [Meta Llama 2](#tab/llama-two)

| **Package**       | **Sample Notebook**                             |
|----------------|----------------------------------------|
| OpenAI SDK (experimental)    | [openaisdk.ipynb](https://github.com/Azure/azureml-examples/blob/main/sdk/python/foundation-models/llama2/openaisdk.ipynb)                                    |
| LangChain      | [langchain.ipynb](https://github.com/Azure/azureml-examples/blob/main/sdk/python/foundation-models/llama2/langchain.ipynb)                                |
| WebRequests     | [webrequests.ipynb](https://github.com/Azure/azureml-examples/blob/main/sdk/python/foundation-models/llama2/webrequests.ipynb)                                 |
| LiteLLM SDK    | [litellm.ipynb](https://github.com/Azure/azureml-examples/blob/main/sdk/python/foundation-models/llama2/litellm.ipynb) |

---

## Cost and quotas

### Cost and quota considerations for Meta Llama models deployed as a serverless API

Meta Llama models deployed as a serverless API are offered by Meta through Azure Marketplace and integrated with Azure Machine Learning studio for use. You can find Azure Marketplace pricing when deploying or fine-tuning models.

Each time a workspace subscribes to a given model offering from Azure Marketplace, a new resource is created to track the costs associated with its consumption. The same resource is used to track costs associated with inference and fine-tuning; however, multiple meters are available to track each scenario independently.

For more information on how to track costs, see [Monitor costs for models offered through the Azure Marketplace](../ai-studio/how-to/costs-plan-manage.md#monitor-costs-for-models-offered-through-the-azure-marketplace).

:::image type="content" source="media/how-to-deploy-models-llama/costs-model-as-service-cost-details.png" alt-text="A screenshot showing different resources corresponding to different model offerings and their associated meters." lightbox="media/how-to-deploy-models-llama/costs-model-as-service-cost-details.png":::

Quota is managed per deployment. Each deployment has a rate limit of 200,000 tokens per minute and 1,000 API requests per minute. However, we currently limit one deployment per model per project. Contact Microsoft Azure Support if the current rate limits aren't sufficient for your scenarios.

### Cost and quota considerations for Meta Llama models deployed managed compute

For deployment and inferencing of Meta Llama models with managed compute, you consume virtual machine (VM) core quota that is assigned to your subscription on a per-region basis. When you sign up for Azure Machine Learning studio, you receive a default VM quota for several VM families available in the region. You can continue to create deployments until you reach your quota limit. Once you reach this limit, you can request a quota increase.  

## Content filtering

Models deployed as a serverless API are protected by Azure AI content safety. When deployed to managed compute, you can opt out of this capability. With Azure AI content safety enabled, both the prompt and completion pass through an ensemble of classification models aimed at detecting and preventing the output of harmful content. The content filtering system detects and takes action on specific categories of potentially harmful content in both input prompts and output completions. Learn more about [Azure AI Content Safety](/azure/ai-services/content-safety/overview).

## Related content

- [Model Catalog and Collections](concept-model-catalog.md)
- [Deploy and score a machine learning model by using an online endpoint](how-to-deploy-online-endpoints.md)
- [Plan and manage costs for Azure AI Studio](../ai-studio/how-to/costs-plan-manage.md)
- [Region availability for models in serverless API endpoints](concept-endpoint-serverless-availability.md)
