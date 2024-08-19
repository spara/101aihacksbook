# Ollama container

Ollama lets you run LLMs on your computer. You can install Ollama locally, but because it is under development you'll have to upgrade frequently to use the latest features. A work around to frequent upgrades is to run Ollama in a container using an orchestration tool such as `docker compose`. A compose file is a set of instructions that tells Docker how to run the container.

This is a basic compose file for running Ollama. A couple of things to note:

- the `image` tag downloads or pulls the latest containerized version of ollama from the Docker Hub repository.
- the `ports` tag defines the port for accessing the container
- the `volumes` tag sets the local directory that the container uses to store files such as the large language model. Note that Docker uses current directory as the root directory.

```yaml
name: ollama
services:
  autollama:
    container_name: ollama
    image: ollama/ollama
    networks:
      default: null
    ports:
      - mode: ingress
        target: 11434
        published: "11434"
        protocol: tcp 
    volumes:
      - type: bind
        source: .
        target: /root/.ollama
        bind:
          create_host_path: true
networks:
  default:
    name: ollama_default  
```

Open a Powershell terminal in Administrator mode. Start the ollama container using `docker compose` with the `--detach` flag to run it in the background


```ps
docker compose up --detach
```

Now that ollama is running, we can use it to downloads model. In the Powershell terminal run `docker exec` to tell the container to download llama3.1. 

```ps
docker exec -it ollama ollama run llama3.1
```

The `docker exec` command logs you into the container. To exit the container and get back to the Powershell terminal, press Ctrl-d.

To test that ollama is working you can send a web request with Powershell.

```ps
(Invoke-WebRequest -method POST -Body '{"model":"llama3.1", "prompt":"Why is the sky blue?", "stream": false}' -uri http://localhost:11434/api/generate ).Content | ConvertFrom-json
```