---
title: "Asynchronous OggOpus audio file recognition in {{ speechkit-full-name }}"
description: "Follow this guide to use asynchronous OggOpus audio file recognition."
---

# Asynchronously recognizing audio files in OggOpus format

Here are examples of [asynchronous recognition of speech](../transcribation.md) from an audio file using the {{ speechkit-name }} [API v2](transcribation-api.md). These examples use the following parameters:

* [Language](../models.md#languages): Russian
* Audio stream format: [OggOpus](../../formats.md#OggOpus) with an OPUS file
* Other parameters left by default.

You can generate and send a speech recognition request using the [cURL](https://curl.haxx.se) utility or a Python script.

An [IAM token](../../../iam/concepts/authorization/iam-token.md) is used to authenticate the service account. Learn more about [authentication in the {{speechkit-name}} API](../../concepts/auth.md).

## Getting started {#before-you-begin}

{% include [transcribation-before-you-begin](../../../_includes/speechkit/transcribation-before-you-begin.md) %}

If you do not have an OggOpus audio file, you can download a [sample file](https://{{ s3-storage-host }}/doc-files/speech.ogg).

## Perform speech recognition via the API {#recognize-using-api}

{% note warning %}

For two-channel OggOpus audio files, do not specify the number of channels in the `audioChannelCount` parameter.

{% endnote %}

{% list tabs %}

- cURL

   1. [Get a link to an audio file](../../../storage/operations/objects/link-for-download.md) in {{ objstorage-name }}.
   1. Create a file like `body.json` and add the following code to it:

      ```json
      {
          "config": {
              "specification": {
                  "languageCode": "ru-RU"
              }
          },
          "audio": {
              "uri": "<link_to_audio_file>"
          }
      }
      ```

      Where:

      * `languageCode`: [Recognition language](../models.md#languages)
      * `uri`: Link to the audio file in {{ objstorage-name }}. Sample link: `https://{{ s3-storage-host }}/speechkit/speech.opus`.

         The link contains additional query parameters (after `?`) for buckets with restricted access. You do not need to provide these parameters in {{ speechkit-name }} as they are ignored.

      Since OggOpus is the default format, you do not need to specify the audio stream format.

      {% note info %}

      Do not provide the [audioChannelCount](transcribation-api.md#sendfile-params) parameter to specify the number of audio channels. OggOpus files already contain information about the channel count.

      {% endnote %}

   1. Run the created file:

      ```bash
      export IAM_TOKEN=<service_account_IAM_token> && \
      curl -X POST \
          -H "Authorization: Bearer ${IAM_TOKEN}" \
          -d "@body.json" \
          https://transcribe.{{ api-host }}/speech/stt/v2/longRunningRecognize
      ```

      Where `IAM_TOKEN` is the IAM token of the service account.

      Result example:

      ```text
      {
          "done": false,
          "id": "e03sup6d5h1q********",
          "createdAt": "2019-04-21T22:49:29Z",
          "createdBy": "ajes08feato8********",
          "modifiedAt": "2019-04-21T22:49:29Z"
      }
      ```

      Save the recognition operation `id` that you received in the response.

   1. Wait for the recognition to complete. It takes about 10 seconds to recognize one minute of an audio file.
   1. Send a request to [get information about the operation](../../../api-design-guide/concepts/operation.md#monitoring):

      ```bash
      curl -H "Authorization: Bearer ${IAM_TOKEN}" \
          https://operation.{{ api-host }}/operations/<recognition_operation_ID>
      ```

      Result example:

      ```text
      {
       "done": true,
       "response": {
        "@type": "type.googleapis.com/yandex.cloud.ai.stt.v2.LongRunningRecognitionResponse",
        "chunks": [
         {
          "alternatives": [
           {
            "text": "your number is 212-85-06",
            "confidence": 1
           }
          ],
          "channelTag": "1"
         }
        ]
       },
       "id": "e03sup6d5h1q********",
       "createdAt": "2019-04-21T22:49:29Z",
       "createdBy": "ajes08feato8********",
       "modifiedAt": "2019-04-21T22:49:36Z"
      }
      ```

- Python 3

   1. Install the `requests` package using the [pip](https://pip.pypa.io/en/stable/) package manager:

      ```bash
      pip install requests
      ```

   1. Create a file, e.g.,`test.py`, and paste the following code to it:

      ```python
      # -*- coding: utf-8 -*-

      import requests
      import time
      import json

      # Specify your IAM token and the link to the audio file in {{ objstorage-name }}.
      key = '<service_account_IAM_token>'
      filelink = '<link_to_audio_file>'

      POST ='https://transcribe.{{ api-host }}/speech/stt/v2/longRunningRecognize'

      body ={
          "config": {
              "specification": {
                  "languageCode": "ru-RU"
              }
          },
          "audio": {
              "uri": filelink
          }
      }

      header = {'Authorization': 'Bearer {}'.format(key)}

      # Send a recognition request.
      req = requests.post(POST, headers=header, json=body)
      data = req.json()
      print(data)

      id = data['id']

      # Request the operation status on the server until recognition is complete.
      while True:

          time.sleep(1)

          GET = "https://operation.{{ api-host }}/operations/{id}"
          req = requests.get(GET.format(id=id), headers=header)
          req = req.json()

          if req['done']: break
          print("Not ready")

      # Show the full server response in JSON format.
      print("Response:")
      print(json.dumps(req, ensure_ascii=False, indent=2))

      # Only show text from recognition results.
      print("Text chunks:")
      for chunk in req['response']['chunks']:
          print(chunk['alternatives'][0]['text'])
      ```

      Where:

      * `key`: IAM token of the service account
      * `filelink`: Link to the audio file in {{ objstorage-name }}

   1. Run the created file:

      ```bash
      python3 test.py
      ```

{% endlist %}

#### See also {#see-also}

* [{#T}](transcribation-api.md)
* [{#T}](transcribation-lpcm.md)
* [{#T}](batch-transcribation.md)
* [{#T}](../../concepts/auth.md)
