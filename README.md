# Walkthrough: Deploying a Hugging Face Model as a Worker Node on the Allora Network

This guide provides a step-by-step process to deploy a Hugging Face model as a Worker Node within the [Allora Network](https://docs.allora.network/). By following these instructions, you will be able to integrate and run models from Hugging Face, contributing to the Allora decentralized machine intelligence ecosystem.

See [complete walkthrough and instructions here](https://docs.allora.network/devs/workers/walkthroughs/walkthrough-hugging-face-worker).

---
## Components

- **Worker**: The node that publishes inferences to the Allora chain.
- **Inference**: A container that conducts inferences, maintains the model state, and responds to internal inference requests via a Flask application. This node operates with a basic linear regression model for price predictions.

Check the `docker-compose.yml` file for the detailed setup of each component.

## Docker-Compose Setup

A complete working example is provided in the `docker-compose.yml` file.

### Steps to Setup

1. **Clone the Repository**
2. **Copy and Populate Configuration**
    
    Copy the example configuration file and populate it with your variables:
    ```sh
    cp config.example.json config.json
    ```

3. **Initialize Worker**
    
    Run the following commands from the project's root directory to initialize the worker:
    ```sh
    chmod +x init.config
    ./init.config
    ```
    These commands will:
    - Automatically create Allora keys for your worker.
    - Export the needed variables from the created account to be used by the worker node, bundle them with your provided `config.json`, and pass them to the node as environment variables.

4. **Faucet Your Worker Node**
    
    You can find the offchain worker node's address in `./worker-data/env_file` under `ALLORA_OFFCHAIN_ACCOUNT_ADDRESS`. [Add faucet funds](https://docs.allora.network/devs/get-started/setup-wallet#add-faucet-funds) to your worker's wallet before starting it.

5. **Start the Services**
    
    Run the following command to start the worker node, inference, and updater nodes:
    ```sh
    docker compose up --build
    ```
    To confirm that the worker successfully sends the inferences to the chain, look for the following log:
    ```
    {"level":"debug","msg":"Send Worker Data to chain","txHash":<tx-hash>,"time":<timestamp>,"message":"Success"}
    ```

## Testing Inference Only

This setup allows you to develop your model without the need to bring up the offchain worker. To test the inference model only:

1. Run the following command to start the inference node:
    ```sh
    docker compose up --build inference
    ```

2. Send requests to the inference model. For example, request ETH price inferences:
    
    ```sh
    curl http://127.0.0.1:8000/inference/ETH
    ```
    Expected response:
    ```json
    {"value":"2564.021586281073"}
    ```

3. Update the node's internal state (download pricing data, train, and update the model):
    
    ```sh
    curl http://127.0.0.1:8000/update
    ```
    Expected response:
    ```sh
    0
    ```
