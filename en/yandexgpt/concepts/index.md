---
title: "About {{ yagpt-full-name }}"
description: "{{ yagpt-full-name }} is the API of a neural network geared to address various needs related to creating text content. {{ yagpt-name }} can generate product descriptions, articles, news stories, newsletters, blog posts, and many other types of texts. The quality of the neural network's response depends directly on the accuracy of the instructions you provide. The more specific your request, the more likely you are to get the result you anticipate."
---

# About {{ yagpt-full-name }}

{% include notitle [preview-stage](../../_includes/yandexgpt/preview.md) %}

{{ foundation-models-full-name }} combines several large generative neural networks and helps you leverage their capabilities for your business tasks.

The {{ yagpt-name }} neural network is geared to address various needs related to creating text content. {{ yagpt-full-name }} can generate product descriptions, articles, news stories, newsletters, blog posts, and many other types of texts. The quality of the neural network's response depends directly on the accuracy of the instructions you provide. The more specific your request, the more likely you are to get the result you anticipate.

The service is dynamically evolving with constant enhancements and refinements to its functionality. You can use {{ yagpt-full-name }} to create text content, such as product descriptions, articles, news stories, newsletters, blog posts, and many other texts. 

## {{ yagpt-full-name }} operating modes {#working-mode}

In {{ yagpt-full-name }}, you can send requests in synchronous and asynchronous modes. In synchronous mode, the service will process your request and respond to it immediately upon receipt. This mode is suitable if you need to maintain a chat bot dialog. In asynchronous mode, the service will receive a request and immediately return its ID you can use to get a response. It will take longer to generate text but responses will be cheaper and of higher quality. Use asynchronous mode if you are not after urgent responses.

Neither mode can currently process unlimited amounts of data. As of now, the maximum total number of [tokens](tokens.md) allowed per user request and model response is {{ yagpt-max-tokens }}. For more information about {{ yagpt-name }} limits, see [{#T}](limits.md).

From the [management console]({{ link-console-main }}), you can access {{ yagpt-name }} Playground. You can use it to test the {{ yagpt-name }} Lite model by submitting to it synchronous requests of these two types:

* **Prompt mode**, where you send a prepared _prompt_ (i.e., an instruction and a request) to the model and receive a response. The model treats each new question as a stand-alone task without saving the context of previous requests.

* **Chat** mode, where you chat with the model, specifying tasks and supplementing your previous remarks. The communication context is transmitted in every message and maintained during the current session until you explicitly start a new one.

You can send both prompt-mode messages and chat-mode messages via the updated API.
