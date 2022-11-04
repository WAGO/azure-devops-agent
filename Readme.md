# Start the agent

- [Check prerequisites](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/v2-linux?view=azure-devops#check-prerequisites)

- Create a copy of the environment file template and update it with your required agent settings:

    ```
    cp .env.template .env
    code .env
    ```
- Pull the docker image:
    ```
    docker compose pull
    ```
- Alternatively you can build the docker image with:
    ```
    docker compose build
    ```
- Start the agent:
    ```
    docker compose up -d
    ```

