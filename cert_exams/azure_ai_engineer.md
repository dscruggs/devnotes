# Azure AI Engineer Associate

My notes are messy and intended for personal use. Here's a high-level summary from ChatGPT on how to study for this cert:

- **Understand Azure AI Fundamentals**
  - Get familiar with basic concepts of AI, machine learning, and cognitive services.
  - Explore the Azure AI platform and its various services and capabilities.
  - Review Azure subscription and resource management.
- **Analyze Solution Requirements and Design AI Solutions**
  - Study techniques for gathering and analyzing requirements for AI solutions.
  - Learn to design AI solutions using Azure services, considering scalability, security, and cost.
  - Understand how to recommend appropriate AI technologies and tools for specific scenarios.
  - Review best practices for data storage, data access, and data preparation.
- **Implement and Monitor AI Solutions**
  - Explore the process of implementing AI models using Azure Machine Learning.
  - Learn how to integrate AI services into applications and systems.
  - Understand techniques for monitoring and troubleshooting AI solutions.
  - Review strategies for ensuring solution reliability, scalability, and compliance.
- **Work with Azure Cognitive Services**
  - Gain knowledge about various Azure Cognitive Services, including Computer Vision, Speech Services, Language Understanding (LUIS), and others.
  - Understand how to use Cognitive Services APIs and SDKs for tasks like image recognition, speech-to-text, sentiment analysis, and language translation.
- **Work with Azure Machine Learning**
  - Study Azure Machine Learning concepts, including data preprocessing, model training, and deployment.
  - Learn how to build and train machine learning models using Azure Machine Learning Designer or code-based approaches.
  - Understand how to deploy and manage machine learning models in Azure.
- **Work with Azure Bot Service and Natural Language Processing**
  - Explore Azure Bot Service and its capabilities for creating conversational AI experiences.
  - Understand how to design and build chatbots using the Bot Framework and Azure Bot Service.
  - Learn about natural language processing (NLP) techniques and how to apply them in chatbot development.
- **Work with Computer Vision and Other AI Workloads**
  - Gain knowledge about Azure services for computer vision, such as Azure Cognitive Services and Azure Custom Vision.
  - Understand techniques for image and video analysis, object detection, and image classification.
  - Study other AI workloads like speech analytics, text analytics, and recommendation systems.
- **Practice and Hands-On Experience**
  - Practice with sample questions and exam simulations to familiarize yourself with the exam format.
  - Engage in hands-on exercises and labs to gain practical experience with Azure AI services.
  - Implement end-to-end AI solutions using Azure services and solve real-world scenarios.
- **Review and Exam Preparation**
  - Review all the topics covered in the exam objectives and ensure your understanding.
  - Use official Microsoft documentation, learning paths, and online training courses for in-depth preparation.
  - Identify areas where you need more study and allocate additional time accordingly.
  - Take practice exams to assess your readiness and identify areas for improvement.

My notes:

## Azure Cognitive Services

|Language| Speech |Vision |Decision|
|:--------------|:------------------:|:------:|:---:|
Text analysis |Speech to Text |Image analysis |Anomaly detection|
|Question answering |Text to Speech |Video analysis |Content moderation|
|Language understanding| Speech Translation| Image classification |Content personalization|
|Translation| Speaker Recognition| Object detection |
|||Facial analysis||
|||Optical character recognition |

### Azure Resources

- `Single-service resource` - each cognitive service is provisioned individually, so you can have separate endpoints for each service.
  - These are good if you want to manage each service independently, and bill seperately. Single service also usually offer a free tier so its good for experimentation
- `Multi-service resource` - allows multiple services on the same endpoint. Only need one set of access credentials
- `Training and Prediction` - Some services require separate training and prediction resources, some can be the same
  - Generally you make a dedicated service-specific resource for training, then a generic cognitive serivces resource to make the model available to applications for inference

### Endpoints

Applications require there to access endpoints:

- `endpoint URI` (HTTP address)
- `subscription key` that client applications must provide to use the service. Keys are created when applications are created and can be regenerated
- `Resource location` (data center). Most SDKs use the endpoint URI, but some require a location

### Security

- Each resource gets 2 keys so you can refresh one and switch all services over to it without disrupting production

### Monitoring

- `Alerts` tab in Cognitive services is used to set up alerts. An alert rule must be specified which includes:
  - scope (which service to monitor)
  - condition based on a **signal type**
    - signal types can be an Activity Log (entry in an activity log based on events related to the resource), or a Metric
  - Optional Actions
  - Alert rule details like the name of the alert and resource group its defined in

### Azure Monitor

Collects metrics for Azure resources on regular intervals. The metrics tracked vary by resource.

For Cognitive Services metrics include endpoint requests, data collected and returned, errors, and others.

The `Metrics` tab of a resource can be used to add metrics to the page and perform various aggregations and analytics

`Dashboards` can be made to add up to 100 named dashboards to show different metrics across services

### Diagnostic Logging

`Azure Event Hub` can be used as a destination for log data and can forward logs to a third party provider or a couple options in Azure:

`Azure Log Analytics` is a service that lets you query and visualize log data in azure portal

`Azure Storage` can be used to store log archives

You can configure diagnostics in the diagnostics tab and you need to specify:

- A name for the diagnostics
- categories of log event data to capture
- details of the destinations to store the logs

### Containers for Cognitive Services

- Can use containers on a Docker server, `Azure Container Instance`, or an `Azure Kubernetes Service`
- Can register container images in `Microsoft Container Registry`

Deploying a Cognitive Services container images to a host requires 3 settings (in the environment variables of the container instance):

- ApiKey (from deployed Azure Cognitive Service which is used for billing)
- Billing (endpoint URI of the deployed service)
- Eula (value of accept to show you accept license agreement)

```docker
# this can be used to deploy the text analytics language container to your local machine
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/language Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
```

Applications can consume this new containerized endpoint rather than the default Azure endpoint. The client application has to be configured with this endpoint but doesn't need to provide a subscription key. Implementing your own authentication solution and applying network security restrictions as required is necessary

### Azure Applied AI Services

Out of the box solutions for common scenarios:

- `Azure Form Recognizer` - an optical character recognition (OCR) solution that can extract semantic meaning from forms, such as invoices, receipts, and others.
- `Azure Metrics Advisor` - A service built on the Anomaly Detector cognitive service that simplifies real-time monitoring and response to critical metrics.
- `Azure Video Analyzer for Media` - A comprehensive video analysts solution build on the Video Indexer cognitive service.
- `Azure Immersive Reader` - A reading solution that supports people of all ages and abilities.
- `Azure Bot Service` - See detailed section
- `Azure Cognitive Search` - See detailed section

#### Azure Bot Service

Azure cloud service for delivering conversational AI solutions, or bots.

- `Bot Framework SDK` can be used to make bots using code
- `Bot Framework Composer` can be used to make bots using a GUI

- Bot templates
  - empty bot (basic skeleton bot)
  - Echo bot (repeats back messages)
  - Core Bot (can integrate w/ language understanding service)

- Terms
  - **Activities** which are events like a user joining or receiving a message
  - ***Turn** is a two way exchange between the user and bot
  - **Process Activity** is called to notify the bot of actions
  - **Turn Handler** method handles and calls logic from actions
  - **Activity Handlers** are event methods that can be overriden to handle different activities
  - **Dialogs** are complex patterns for handling stateful, multi-turn convos

- Azure resource creation
  - Develop bot
  - Register and Azure App
  - Create bot application service
  - Prepare bot for deployment w/ config files and dependencies
  - deploy bot as weeb app

- `Bot Framework Emulator` for testing bot locally
- `Bot Framework Composer` IDE for building bots, very visual. Has embedded web chat for teaching bot there

- `Power Virtual Agents` is a no code chatbot building service, but some features require using the Bot Framework Composer to integrate.

- Text, buttons, images, and cards can be implemented on a bot

- dialog elements:
  - Recognizer determines user intent
  - Trigger responds to a detected intent
  - Language Generator formulates output from the bot to the user

#### Azure Cognitive Search

A cloud-scale search solution that uses cognitive services to extract insights from data and documents. Can ingest and index data from various sources

- Can define `enrichment pipelines` to use AI skills to enhance the index with insights derived from the source data. Examples:
  - use computer vision and NLP to generate descriptions of images and extract text from scanned documents
  - determine key phrases from documents
- `knowledge stores` can persist insights from enrichment pipelines for further analysis and integrations into data pipelines

### Azure Cognitive Services for Language

#### Text Analysis

Basically their NLP module. Areas include:

- `Language detection` - determining the language in which text is written.
  - Confidence scores indicate level of confidence on 0-1. Multi-lingual documents will give an answer on which language is the most prevelant, but confidence will be lower
  - items that lanuage is ambiguous or there are errors will return **NaN** for the score and **(Unknown)** for the language
- `Key phrase extraction` - identifying important words and phrases in the text that indicate the main points.
- `Sentiment analysis` - quantifying how positive or negative the text is.
  - could be positive, negative, or mixed. Gives scores 0-1 for each and then gives an overall sentiment prediction
- `Named entity recognition` - detecting references to entities, including people, locations, time periods, organizations, and more.
  - **Entities:** Person, Location, DateTime, Organization, Address, Email, URL
  - gives confidence scores for each. Response is a list for each document
- `Entity linking` - identifying specific entities by providing reference links to Wikipedia articles.
  - gives confidence score w/ luink to wikipedia

For each of these `Documents` are a collection of phrases you can pass to the language detection model. Document size must be under 5120 characters, and collections can only be 1000 IDs

#### Language Understanding

`Ocp-Apim-Subscription-Key` is used for the REST API

- Need to define intents, utterances. Every model needs a None utterance to capture possible user submissions that require no action (like "hello")
- `Entities` can be used to give context to utterances (like dates, times, locations, etc.). There are prebuilt ones in Azure or you can build your own. **list** entities can be made that have a specific set of values (like days of the week)

Process to build a model:

1. Train a model to learn intents and entities from sample utterances.
2. Test the model interactively or using a testing dataset with known labels
3. Deploy a trained model to a public endpoint so client apps can use it
4. Review predictions and iterate on utterances to train your model

- `Summarization` can be used in both documents (extractive) and conversations (converational)
- `Named Entity Recognition` is a thing
- `Personally Identifiable Information Detection`
- `Sentiment Analysis`

Can train custom models for `Conversational Language Understanding`, `NER`, `text classification`, `Question Answering`

Need **kind** (model type), **parameters** like projectname and deploymentname or modelversion, and **analysis input** being the actual data predicted on

Can use containers

#### Translation

- `Language Detection` - same as text analysis, I'm not sure if its as robust
- `Translation` - can translate into multiple languages at once and returns as list
  - Options:
    - `word alignment` shows a mapping of which characters in the original text map to which characters in the translated text
    - `sentence length` returns length of both the source and translated sentences
    - `profanity filtering` can be done by setting the option to **NoAction** (translate as is), **Deleted** (remove them), or **Marked** (puts asterisk on them)
  - Can train a custom model if you have enough specific business terms or language to learn

- `Transliteration` - converts things like japanese and others into latin text

### Azure Cognitive Serivces for Speech

- `Speech to text`: An API that enables speech recognition in which your application can accept spoken input.
  - **Speech to text** is standard, **Speech to text Short Audio** is optimized for short audio clips (60 seconds or less)
  - need to pass a `SpeechConfig` object which is a location and key for the resource
  - An optional `AudioConfig` object can also be passed to specify the input. By default its the default system microphone but can be another input or an audio file. These two combined are a `SpeechRecognizer` object.
- `Text to speech`: An API that enables speech synthesis in which your application can provide spoken output.
  - **text to speech** is standard, **text to speech long audio** is optimized for short audio clips (60 seconds or less)
  - default output is speakers, but can be changed with `AudioConfig`. `SpeechConfig` has the location and key. These are used to make a `SpeechSynthesizer` Object
  - Can set audio format and voices used (there's lists of standard and neural voices to choose from)
  - `Speech Synthesis Markup Language (SSML)` can be used to modify how the voice comes out in ways like tone, pronunciation, pausing, or even insert other speech or audio. This would be done on the text you're having read
- `Speech Translation`: An API that you can use to translate spoken input into multiple languages.
  - Similar to speech recognition, but there's additional config for source and target languages in a `SpeechTranslationConfig`
  - Speech to text or text to speech is available. Can also do Speech to speech
    - `Event-based synthesis`: need to create an event handler for the TranslationRecognizer object's Synthesizing event and then use the GetAudio() method of the result parameter to retrieve to bytestream of the translated audio. SDK will have ways to implement in docs
    `Manual Synthesis`: You can just use the TranslationRecognizer to translate audio into text in one or more languages, and then you can use a SpeechSynthesizer to synthesize the audio

- `Speaker Recognition`: An API that enables your application to recognize individual speakers based on their voice.
- `Intent Recognition`: An API that integrates with the **Language Understanding** service to determine the semantic meaning of spoken input.

You need a location (EastUS) and a key for the assigned resource to use this with an SDK.

### Azure Cognitive Services for Vision

#### Image Analysis

- Use cases:
  - `Description and tag generation` - determining an appropriate caption for an image, and identifying relevant "tags" that can be used as keywords to indicate its subject.
  - `Object detection` - detecting the presence and location of specific objects within the image.
  - `Face detection` - detecting the presence, location, and features of human faces in the image.
  - `Image metadata`, color, and type analysis - determining the format and size of an image, its dominant color palette, and whether it contains clip art.
  - `Category identification` - identifying an appropriate categorization for the image, and if it contains any known landmarks.
  - `Brand detection` - detecting the presence of any known brands or logos.
  - `Moderation rating` - determine if the image includes any adult or violent content.
  - `Optical character recognition` - reading text in the image.
  - `Smart thumbnail generation` - identifying the main region of interest in the image to create a smaller "thumbnail" version.

- Color Scheme Detection colors:
  - black, blue, brown, gray, green, orange, pink, purple, red, teal, white, and yellow

#### Video Analyzer for Media

- Use Cases
  - `Facial recognition` - detecting the presence of individual people in the image. This requires Limited Access approval.
  - `Optical character recognition` - reading text in the video.
  - `Speech transcription` - creating a text transcript of spoken dialog in the video.
  - `Topics` - identification of key topics discussed in the video.
  - `Sentiment` - analysis of how positive or negative segments within the video are.
  - `Labels` - label tags that identify key objects or themes throughout the video.
  - `Content moderation` - detection of adult or violent themes in the video.
  - `Scene segmentation` - a breakdown of the video into its constituent scenes.

Can create custom models for:

- People, Language, Brands, Animated Characters

Need to index it before analyzing

#### Training a Custom Vision Service

- Can use the `Custom Vision portal` or the Custom Vision rest API or SDK, or a combination
  - has smart labeling options
- Writing code with the SDK and APIs allows you to automate the training process
- Labeling tools are in Azure Machine Learning Studio and the Microsoft Visual Object Tagging Tool is useful for tagging as well

#### Tiers / Pricing

- Free
- S1
- S2
- S3
- Standard

#### Face Service

Use Cases:

- `Face detection` - for each detected face, the results include an ID that identifies the face and the bounding box coordinates indicating its location in the image.
- `Face attribute analysis` - you can return a wide range of facial attributes, including:
  - Head pose (pitch, roll, and yaw orientation in 3D space)
  - Glasses (NoGlasses, ReadingGlasses, Sunglasses, or Swimming Goggles)
  - Blur (low, medium, or high)
  - Exposure (underExposure, goodExposure, or overExposure)
  - Noise (visual noise in the image)
  - Occlusion (objects obscuring the face)
- `Facial landmark location` - coordinates for key landmarks in relation to facial features (for example, eye corners, pupils, tip of nose, and so on)
- `Face comparison` - you can compare faces across multiple images for similarity (to find individuals with similar facial features) and verification (to determine that a face in one image is the same person as a face in another image)
- `Facial recognition` - you can train a model with a collection of faces belonging to specific individuals, and use the model to identify those people in new images.

#### Reading Text

Options:

- The Read API:
  - Use this API to read small to large volumes of text from images and PDF documents.
  - This API uses a newer model than the OCR API, resulting in greater accuracy.
  - The Read API can read printed text in multiple languages, and handwritten text in English.
  - The initial function call returns an asynchronous operation ID, which must be used in a subsequent call to retrieve the results.
  - Has over 160 languages

- The Image Analysis API:
  - Currently still in preview, with reading text functionality added version 4.0.
  - Use this API to read small amounts of text from images.
  - Returns contextual information, including line number and position.
  - Results are returned immediately (synchronous) from a single function call.
  - Has functionality for analyzing images past extracting text, including detecting content (such as brands, faces, and domain-specific content), describing or categorizing an image, generating thumbnails and more.

#### Form Recognizer

- `Document analysis models`: which take an input of JPEG, PNG, PDF, and TIFF files and return a JSON file with the location of text in bounding boxes, text content, tables, selection marks (also known as checkboxes or radio buttons), and document structure.

- `Prebuilt models`: which detect and extract information from document images and return the extracted data in a structured JSON output. Form Recognizer currently supports prebuilt models for several forms, including:
  - W-2 forms
  - Invoices
  - Receipts
  - ID documents
  - Business cards

- `Custom models`: custom models extract data from forms specific to your business. Custom models can be trained by calling the Build model API, or through Form Recognizer Studio
- The file size must be less than 500 MB for paid (S0) tier and 4 MB for free (F0) tier
- The total size of the training data set must be 500 pages or less
- Form Recognizer Studio for easier training

### Azure Cognitive Search

- Creates indexes for data and allows complex queries on it

- Query process:
  - query parsing
  - Lexical analysis
  - document retrieval
  - scoring/ranking

- Projections
  - `Object projections` are JSON representations of the indexed documents.
  - `File projections` are JPEG files containing image data extracted from documents.
  - `Table projections` create a relational schema for the extracted data.

`AML Skills` can be defined as custom for Azure Search and used with a web service endpoint

## Azure Machine Learning

Azure MLOps Platform

- AutoML
- No-code ML designer
- MLOps pipelines
- cloud compute access for workloads + experiments

## Azure Virtual Networks

## Responsible AI

## CICD

## Cost

- `Cost Analysis` tab shows cost for various services incurred, included Cognitive Services

## Need to learn

- Azure COntent Moderator
- Personalizer
- Azure Metrics Advisor
- Azure Immersive Reader
- Azure Video Indexer
-
