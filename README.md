# Chainlink Oracle Node Tutorial 
> Learn about Chainlink by working through basic examples using Ethereum Sepolia, Arbitrum Sepolia, or Polygon Amoy.

## Deploy a Chainlink Node

### Run with Docker

#### Requirements
- Install [Docker](https://docs.docker.com/get-docker/)  
- Create an [Infura](https://infura.io/) account:  
  1. Go to the **Projects** section and create a new project.  
  2. In the **KEYS** section, note your credentials.  
  3. Under the **ENDPOINTS** tab, select **Ethereum Sepolia**, **Arbitrum Sepolia**, or **Polygon Amoy**.  
  4. Copy the HTTPS and WebSocket URLs for each network.  
     - Example for Ethereum Sepolia:  
       - `https://sepolia.infura.io/v3/YOUR_API_KEY`  
       - `wss://sepolia.infura.io/ws/v3/YOUR_API_KEY`  
     - Example for Arbitrum Sepolia:  
       - `https://arbitrum-sepolia.infura.io/v3/YOUR_API_KEY`  
       - `wss://arbitrum-sepolia.infura.io/ws/v3/YOUR_API_KEY` 
     - Example for Polygon Amoy:  
       - `https://polygon-amoy.infura.io/v3/YOUR_API_KEY`  
       - `wss://polygon-amoy.infura.io/ws/v3/YOUR_API_KEY`

These will be used to connect your Chainlink node to the blockchain via RPC.

---

#### Running the Node with Docker


1. **Set up the environment file for the database**  
   The project contains two folders for network-specific configurations: **Ethereum Sepolia**, **Arbitrum Sepolia**, or **Polygon Amoy**.   
   Navigate to the folder for the network you want to run. For example:

   ```bash
   cd Ethereum
   ```
   Inside this folder, create a database.env file with the following values

   ```env
   POSTGRES_USER=postgres
   POSTGRES_PASSWORD=mysecretpassword
   POSTGRES_DB=chainlink-sepolia
   ```
   - POSTGRES_USER — PostgreSQL username.
   - POSTGRES_PASSWORD — password to access the database.
   - POSTGRES_DB — name of the PostgreSQL database instance.



2. **Modify `.api` file for Oracle Node authentication**
In the `chainlink-volume` folder, edit the `.api` file to contain your login credentials for the Oracle Node: These details serve as the login username and password for the Oracle Node. They are required only during the initial creation of the PostgreSQL database instance but will remain valid for its entire lifecycle. The email must contain an @ symbol, and the password must be between 16 and 50 characters long.
 
    ```api
    sampleemail@email.com
    mysecretpassword
    ```


3. **Modify `config.toml` file**
In the `chainlink-volume` folder, update the config.toml file to match the appropriate ChainID (e.g., 11155111 for Ethereum Sepolia). Replace the WSURL and HTTPURL values with the endpoints provided by your Infura account. Below is an example configuration for Ethereum Sepolia:

    ```toml
    [Log]
    Level = 'warn'

    [WebServer]
    AllowOrigins = '\*'
    SecureCookies = false

    [WebServer.TLS]
    HTTPSPort = 0

    [[EVM]]
    ChainID = '11155111'

    [[EVM.Nodes]]
    Name = 'Sepolia'
    WSURL = 'wss://sepolia.infura.io/ws/v3/YOUR_API_KEY'
    HTTPURL = 'https://sepolia.infura.io/v3/YOUR_API_KEY'
    ```


4. **Modify `secret.toml` file**
In the `chainlink-volume` folder, update the keystore secret key if necessary. This key serves as the wallet password used to unlock the generated keystore file. Also, update the database URL to ensure that the mysecretpassword and chainlink-sepolia values match the credentials and database name you set in step 1 for the PostgreSQL instance.

    ```toml
    [Password]
    Keystore = 'mysecretkeystorepassword'
    [Database]
    URL = "postgresql://postgres:mysecretpassword@host.docker.internal:5432/chainlink-sepolia?sslmode=disable"
    ```


5. **Run Docker instances**
From one directory above the `chainlink-volume` folder, the setup is automated using the `docker-compose.yml` file. This file defines two services
    - chainlink_db: Runs a PostgreSQL database with environment variables from database.env, stores data locally, and includes a health check.
    - chainlink: Runs the Chainlink node (v2.26.0) after the database is healthy, using configs and secrets from mounted volumes, exposing port 6688, and connecting to the host network.
    It essentially provisions a ready-to-run Chainlink node with a persistent Postgres backend. Run the following syntax in Bash to run docker instances.

    ```bash
    docker compose up
    ```
    - Open `http://localhost:6688` in your browser to access the Chainlink node login page.
    - Sign in using the credentials from your `.api` file.
    - To find your account address, click the Configuration tab in the top‑right corner and look for the ACCOUNT_ADDRESS value.
    - Fund your address with test LINK and ETH/POL by copying the account address and visiting the [Chainlink Faucet](https://faucets.chain.link/) .


## References

The work contained in this directory is based on the official instructions provided in the [Chainlink Nodes documentation](https://docs.chain.link/chainlink-nodes).  
It follows the recommended setup and configuration steps for running a Chainlink node, adapted with network‑specific examples for Ethereum Sepolia, Arbitrum Sepolia, and Polygon Amoy.  
All configurations, environment variables, and deployment processes have been aligned with the guidelines outlined in the documentation to ensure compatibility and best practices.