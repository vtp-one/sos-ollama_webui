# sos-ollama_webui

SOS System for Ollama Open-WebUI

Open WebUI is an extensible, feature-rich, and user-friendly self-hosted WebUI designed to operate entirely offline. 
It supports various LLM runners, including Ollama and OpenAI-compatible APIs. 

For more information about Ollama Open-WebUI:
 - [Ollama Open-Webui](https://github.com/open-webui/open-webui.git)


The SOS-Toolkit version includes the usage of an SOS-Toolkit Ollama Service. 

During Installation the System will not install any models, but it is possible to add them if desired. 
Check out the configuration section of this readme for more information.


# Requirements
 - [SOS-Toolkit](https://github.com/jk-vtp-one/sos-toolkit)

Follow the instructions to get a working SOS-Toolkit virtual environment. 

Intallation instructions assume you already have an activated virtual environemnt. 

All Commands in the environment are prefaced with: `(sos)$`


# Current Release Target
```
v0.0.1
```


# Install
These steps will pull, install, and run SOS-ollama_webui. 

You should start from your SOS-Toolkit root directory: `/sos` or it's equivalent in your environment

:warning: WARNING :warning:
```
A SYSTEM CAN RUN ANY TERMINAL COMMAND AND ANY ARBITRARY PYTHON CODE INCLUDING DYNAMICALLY GENERATED CODE
DO NOT INTERACT WITH SYSTEMS THAT YOU DO NOT KNOW, TRUST, OR HAVE THE CAPABILITY TO MANUALLY VERIFY THEIR OPERATION
```

### 1. Pull the sos-ollama_webui repository and enter it
```
(sos)$ sos-toolkit pull https://github.com/jk-vtp-one/sos-ollama_webui --remote-branch v0.0.1
(sos)$ cd sos-ollama_webui
```

### 2. Setup the System
```
(sos)$ sos-toolkit setup
```

### 3. Install the System
```
(sos)$ sos-toolkit install
```

### 4. Run the System
```
(sos)$ sos-toolkit up
```

This command will output two system status messages during startup, one for the SOS-Toolkit Ollama Service and one for the Open-WebUI.
```
{'terminal.print_object': 'OLLAMA RUNNING ON: http://sos-ollama:11434'}
```
```
{'terminal.print_object': 'OLLAMA-WEBUI RUNNING ON: http://0.0.0.0:3000'}
```
The address for the Ollama Service represents the internal docker-network address for it. 
Other containers attached to it's network can access it using that address, but from outside that network you can use the localhost address or the local machine IP address: http://0.0.0.0:3000

When you connect to the OpenWebUI from a browser, you will first need to setup an account for the instance. 
Click Sign Up, and enter any information you want - it doesn't actually use the email for anything besides a login. 
This account information is persisted across system restarts by using the SOS-Toolkit local_data Service. 
The data is stored in:
```
<system path>/internal/local_data/webui
```

For more information about how to use OpenWebui, look at their documentation: [Ollama Open-Webui](https://github.com/open-webui/open-webui.git)

What we are concerned with is the ability to download and persist models. 
The default configuration the system provides for the SOS-Toolkit Ollama Service is to save model data at:
```
<system path>/internal/local_data/ollama_data
```
You can change the location of this directory using configuration options which are detailed in the next section. 

### 5. Shutdown the System
```
(sos)$ sos-toolkit down
```


# Configuration
This system includes two configuration options. 

### Auto-Install Models
The first configuration is the ability to have the SOS-Toolkit install command automatically download models from the Ollama Library. 
Currently, the only way to set this option is by using the `sos-local.yaml` file. 
There isn't yet a good way to use the `sos-toolkit config` command to create lists of objects, which forces us to have to enter them manually.

Create an `sos-local.yaml` file in the system repository.
```
(sos)$ nano sos-local.yaml
```

This is a YAML file, so you need to make sure to use correct formatting. 
The object we are creating is expected to be either a single string value, or a list (don't complain about the list part of the name, it's only a demo). 

For now we will just set it to a string of a single model to download, but you can add a list of models using YAML syntax.
Add this to the `sos-local.yaml` file and save it.
```
namespace:
  model_list: 
    - tinyllama:1.1b-chat-v0.6-q2_K
```
Find other models at [ollama-library](https://ollama.com/library)

TinyLlama is exactly that, a tiny version of the llama LLM: [Ollama Library: TinyLlama](https://ollama.com/library/tinyllama), and we have selected a low-quality version of it to keep the download size small (483MB).

The entered model names must be valid, there are no checks and it will error if wrong, so make sure you enter them correctly.

Our `sos-local.yaml` file will be loaded when we run the `sos-toolkit setup` command and it's data will be merged into the SOSContext object. 
If you have a pre-existing `sos-context.yaml` you will need to either overwrite it using the setup commands overwrite switch, remove it using the sos-toolkit clean command, or manually delete it. 

Here is the overwrite in action:
```
(sos)$ sos-toolkit setup --overwrite
```

The new SOSContext is generated, and overwrites the old. 
This of course deletes anything from the previous `sos-context.yaml`, but that is what we wanted. 

After overwriting the context, we will need to re-run the install command for the system. 
It is during this install step that the system uses the SOS-Toolkit Ollama Service plugin to run a special tool that will parse the value from `context.namespace.model_list` and install the required models.


### Model Data Directory
The second configuration option is the ability to change the location the SOS-Toolkit Ollama Service uses to store downloaded models. 
This needs to be set before we use the install command, otherwise it will use the default location.
```
(sos)$ sos-toolkit config --target service.ollama.data_directory

Input Location for Ollama Data Directory:

```
Enter the target location to persist your Ollama data and press enter. 
Using this method to store data will make it easier to share models between systems and also to persist models outside of the system's internal data. 
You need to provide it with a valid directory, and there are currently no checks on that validity, so be careful and make sure you know what your entering.

If you generate a new `sos-context.yaml` file, you will need to reconfigure this value.

If you have already downloaded models to the default location, you will need to manually move the data to the new location.
The default storage location is:
```
<system_path>/internal/local_data/ollama_data
```


After configuring these options, we can install the system.
```
(sos)$ sos-toolkit install
```
During the install steps, it will now start the SOS-Toolkit Ollama Service in order to pull the models and store them in the directory you provided.
This can take a long time depending on the size of model being downloaded and your download speed.

Run the system and go through the OpenWebUI sign-in and your models are there ready to use.
```
(sos)$ sos-toolkit up
```


# More Information
 - [SOS-Toolkit](https://github.com/jk-vtp-one/sos-toolkit)
 - [SOS-Test_System](https://github.com/jk-vtp-one/sos-test_system)


# License
LICENSE WILL CHANGE

CURRENT LICENSE: VTP-LICENSE-v0.0.1

FOR APPROVED USE: POLYFORM - STRICT
