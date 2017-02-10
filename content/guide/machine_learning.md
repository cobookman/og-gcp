+++
title = "Machine Learning"
categories = ["Machine Learning"]
draft = false

[[owners]]
  email = "bookman@google.com"
  name = "Colin Bookman"

[[owners]]
  email = "jonathancham@google.com"
  name = "Jonathan Cham"
+++
Google Cloud ML Platform provides modern machine learning services, with
pre-trained models and a service to generate your own tailored models. GCP's
neural net-based ML platform has better training performance and increased
accuracy compared to other large scale deep learning systems. Major Google
applications use Cloud Machine Learning, including Photos (image search),
the Google app (voice search), Translate, and Inbox (Smart Reply).
<!--more-->


# Cloud ML [1]

Cloud Machine Learning combines the managed infrastructure of GCP with the power and flexibility of
TensorFlow. Cloud ML mainly does two things:

* Enables you to train machine learning models by running TensorFlow training applications in the
  cloud.
* Hosts those trained models for you in the cloud so that you can use them to get predictions
  about new data.


# Jobs API [2]

The Cloud Jobs API provides job search that anticipates what job seekers are looking for and 
surfaces targeted recommendations that help them discover new opportunities. The Jobs API is 
powered by machine learning, and understands how job titles and skills relate to one another. It 
matches job posting content, location and seniority to the jobseeker's preferences.


# Natural Language API [[3]](#references)[[4]](#references)

The Natural Language API extract information about people, places, events and much more in blocks 
of text. It can be used to understand sentiment about a product on social media or parse intent 
from customer conversations happening in a call center or a messaging app.

The Natural Language API has several methods for performing analysis and annotation on your text. 
Each level of analysis provides valuable information for language understanding. Each API call also 
detects and returns the language, if a language is not specified by the caller in the initial 
request.


## Sentiment Analysis [[4]](#references)

Sentiment analysis inspects the given text and identifies the prevailing emotional opinion within 
the text, especially to determine a writer's attitude as positive, negative, or neutral. 


## Entity Analysis [[4]](#references)

Entity analysis inspects the given text for known entities (proper nouns such as public figures, 
landmarks, etc.) and returns information about those entities.


## Syntactic Analysis [[4]](#references)

Syntactic analysis extracts linguistic information, breaking up the given text into a series of 
sentences and tokens (generally, word boundaries), providing further analysis on those tokens.


## Sample API Response [[4]](#references)

### Request [[4]](#references)

Asks the Natural Language API to analyze a plain text copy of the Gettysburg Address.

```json
{
  "document":{
    "type":"PLAIN_TEXT",
    "language": "EN",
    "gcsContentUri": "gs://cloud-samples-tests/natural-language/gettysburg.txt"
  },
}
```

### Sentiment Analysis Response [[4]](#references)

Sentiment analysis attempts to determine the overall attitude (positive or negative) expressed 
within the text. Sentiment is represented by numerical score and magnitude values. For an 
interpretation of this API response please read [interpreting sentiment analysis 
values](https://cloud.google.com/natural-language/docs/basics#interpreting_sentiment_analysis_values
).


```json
{
  "documentSentiment": {
    "score": 0.2,
    "magnitude": 3.6
  },
  "language": "en",
   "sentences": [
    {
      "text": {
        "content": "Four score and seven years ago our fathers brought forth
        on this continent a new nation, conceived in liberty and dedicated to
        the proposition that all men are created equal.",
        "beginOffset": 0
      },
      "sentiment": {
        "magnitude": 0.8,
        "score": 0.8
      }
    },
   ...
}
```


### Entity Analysis Response [[4]](#references)

Entity analysis returns a set of detected entities, and parameters associated with those entities, 
such as the entity's type, relevance of the entity to the overall text, and locations in the text 
that refer to the same entity. Entities are returned in the order (highest to lowest) of their 
salience scores, which reflect their relevance to the overall text.

```json
{
  "entities": [
    {
      "name": "Lawrence of Arabia",
      "type": "WORK_OF_ART",
      "metadata": {
        "mid": "/m/0bx0l",
        "wikipedia_url": "http://en.wikipedia.org/wiki/Lawrence_of_Arabia_(film)"
      },
      "salience": 0.75222147,
      "mentions": [
        {
          "text": {
            "content": "Lawrence of Arabia",
            "beginOffset": 1
          },
          "type": "PROPER"
        },
        {
          "text": {
            "content": "film biography",
            "beginOffset": 39
          },
          "type": "COMMON"
        }
      ]
    },
    {
      "name": "T.E. Lawrence",
      "type": "PERSON",
      "metadata": {
        "mid": "/m/0bx5v",
        "wikipedia_url": "http://en.wikipedia.org/wiki/T._E._Lawrence"
      },
      "salience": 0.12430617,
      "mentions": [
        {
          "text": {
            "content": "T. E. Lawrence",
            "beginOffset": 94
          },
          "type": "PROPER"
        },
        {
          "text": {
            "content": "Lieutenant",
            "beginOffset": 83
          },
          "type": "COMMON"
        },
        {
          "text": {
            "content": "Lawrence",
            "beginOffset": 145
          },
          "type": "PROPER"
        }
      ]
    },
    {
      "name": "British",
      "type": "LOCATION",
      "metadata": {
        "mid": "/m/07ssc",
        "wikipedia_url": "http://en.wikipedia.org/wiki/United_Kingdom"
      },
      "salience": 0.078094982,
      "mentions": [
        {
          "text": {
            "content": "British",
            "beginOffset": 75
          },
          "type": "PROPER"
        }
      ]
    },
    {
      "name": "film",
      "type": "WORK_OF_ART",
      "metadata": {},
      "salience": 0.033808723,
      "mentions": [
        {
          "text": {
            "content": "film",
            "beginOffset": 161
          },
          "type": "COMMON"
        }
      ]
    },
    {
      "name": "Peter O'Toole",
      "type": "PERSON",
      "metadata": {
        "mid": "/m/0h0jz",
        "wikipedia_url": "http://en.wikipedia.org/wiki/Peter_O'Toole"
      },
      "salience": 0.011568651,
      "mentions": [
        {
          "text": {
            "content": "Peter O'Toole",
            "beginOffset": 110
          },
          "type": "PROPER"
        }
      ]
    }
  ],
  "language": "en"
}
```


### Syntactic Analysis Response [[4]](#references)

Syntactic Analysis consists of the following operations:

* [Sentence extraction](https://cloud.google.com/natural-language/docs/basics#sentence-extraction) breaks up 
  the stream of text into a series of sentences.
* Tokenization breaks the stream of text up into a series of tokens, which usually refer to word 
  boundaries.
* The Natural Language API then processes the tokens and, using their locations within sentences, 
  adds syntactic information to the tokens.

Additionally, tokens are evaluated and placed within a [dependency 
tree](https://cloud.google.com/natural-language/docs/basics#dependency-tree), which allows you to 
determine the syntactic meaning of the tokens, and illustrate the relationship of tokens to each 
other, and their containing sentences.

```json
{
  "sentences": [
    {
      "text": {
        "content": "Four score and seven years ago our fathers brought forth on
                    this continent a new nation, conceived in liberty and
                    dedicated to the proposition that all men are created
                    equal.",
        "beginOffset": 0
      }
    },
    {
      "text": {
        "content": "Now we are engaged in a great civil war, testing whether
                    that nation or any nation so conceived and so dedicated can
                    long endure.",
        "beginOffset": 175
      }
    },
...
...
    {
      "text": {
        "content": "It is rather for us to be here dedicated to the great task
                    remaining before us--that from these honored dead we take
                    increased devotion to that cause for which they gave the
                    last full measure of devotion--that we here highly resolve
                    that these dead shall not have died in vain, that this
                    nation under God shall have a new birth of freedom, and that
                    government of the people, by the people, for the people
                    shall not perish from the earth.",
        "beginOffset": 1002
      }
    }
  ],
  "language": "en"
}

```

# Speech API [[5]](#references)

The Speech API parses text from audio fiels. The API can recognize over 80 languages and variants. 
A few use cases would be: users dictating to an application's microphone, command-and-control 
through voice, or transcribing audio files. 

The Speech API has three main methods to perform speech recognition, Synchronous, Asynchronous, and 
Streaming Recognition.


## Synchronous Recognition [[5]](#references)

Synchronous Recognition (REST and [gRPC](http://www.grpc.io/)) sends audio data to the Speech API, 
performs recognition on that data, and returns results after all audio has been processed. 
Synchronous recognition requests are limited to audio data of 1 minute or less in duration.


## Asynchronous Recognition [[5]](#references)

Asynchronous Recognition (REST and [gRPC](http://www.grpc.io/)) sends audio data to the Speech API 
and initiates a Long Running Operation. Using this operation, you can periodically poll for 
recognition results. Use asynchronous requests for audio data of any duration up to 80 minutes.


## Streaming Recognition [[5]](#references)

Streaming Recognition ([gRPC](http://www.grpc.io/) only) performs recognition on audio data 
provided within a [gRPC bi-directional 
stream](http://www.grpc.io/docs/guides/concepts.html#bidirectional-streaming-rpc). Streaming 
requests are designed for real-time recognition purposes, such as capturing live audio from a 
microphone. Streaming recognition provides interim results while audio is being captured, allowing 
result to appear, for example, while a user is still speaking.


## Sample API Request [[5]](#references)

### Request [[5]](#references)

This sample shows a synchronous speech API request.


```json
{
    "config": {
        "encoding": "LINEAR16",
        "sampleRate": 16000,
        "languageCode": "en-US",
    },
    "audio": {
        "uri": "gs://bucket-name/path_to_audio_file"
    }
}
```


### Response [[5]](#references)

The Synchronous Response and final Asynchronous Response is almost identical. The major difference 
is that Asynchronous API calls will instantly return a long running job id that can be polled for 
job completion status and result. Both of the API calls end result contains the following data.

```json
{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 0.98267895,
          "transcript": "how old is the Brooklyn Bridge"
        }
      ]
    }
  ]
}
```


# Translation API [[6]](#references)

Translates an arbitrary string into any supported language. Translation of source text from the 
source language to a target language (e.g., French to English). Language detection is also 
available In cases where the source language is unknown.


## Standard [[6]](#references)

The Standard edition offers translation in over 100 languages and price-performance that is ideal 
for short, real-time conversational text.


## Premium [[6]](#references)

The Premium edition is tailored for users who need precise, long-form translation services. 
Examples include livestream translations, high volume of emails, and detailed articles and 
documents.


# Vision API [[7]](#references)

Classifies images into thousands of categories (e.g., "sailboat", "lion", "Eiffel Tower"), detects 
individual objects and faces within images, and finds and reads printed words contained within 
images. You can build metadata on your image catalog, moderate offensive content, or enable new 
marketing scenarios through image sentiment analysis. 

{{< image
  src="/images/machine_learning/vision_api_sample.png"
  width="1014"
  height="521"
  alt="vision api sample"
  link="true"
>}}



# Labs {#labs}

[Entity and Sentiment analysis with the Natural Language API](
  https://codelabs.developers.google.com/codelabs/cloud-nl-intro/index.html?index=..%2F..%2Findex#0)

[Classify handwritten digits with Cloud ML](
  https://cloud.google.com/ml/docs/quickstarts/training)

[Make predictions with existing model](
  https://cloud.google.com/ml/docs/quickstarts/prediction)

[Detection with Vision API](
  https://codelabs.developers.google.com/codelabs/cloud-vision-intro/index.html#0)


# Related Technologies {#related-technologies}

Theano

Scikit learn

Caffee

Torch

Amazon Machine Learning - ML platform

Amazon Rekognition - image recognition

Amazon Lex - conversational interfaces

Amazon Polly - speech to text


# References {#references}

[1] https://cloud.google.com/ml/docs/concepts/technical-overview 

[2] https://cloud.google.com/jobs-api/ 

[3] https://cloud.google.com/natural-language/ 

[4] https://cloud.google.com/natural-language/docs/basics 

[5] https://cloud.google.com/speech/docs/basics 

[6] https://cloud.google.com/translate/ 

[7] https://cloud.google.com/vision/ 

