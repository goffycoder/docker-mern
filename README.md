1. Dockerfile in  ``` client/Dockerfile ```
    ```Dockerfile 
    FROM node:14-slim
    WORKDIR /usr/src/app 
    COPY ./package.json ./ 
    COPY ./yarn.lock ./ 
    RUN yarn install 
    COPY . . 
    EXPOSE 3000 
    CMD [ "yarn", "start" ] 
    ```
    <b>Explanation</b>
    1. **Base Image**: Uses Node.js 14 slim image.
    2. **Working Directory**: Sets work directory to `/usr/src/app`.
    3. **Copy Dependencies**: Copies `package.json` and `yarn.lock`.
    4. **Install Dependencies**: Runs `yarn install`.
    5. **Copy Application**: Copies source code.
    6. **Expose Port**: Exposes port 3000.
    7. **Default Command**: Starts app with `yarn start`.

2. Dockerfile in ``` server/Dockerfile ``` 
    ```Dockerfile 
    FROM node:14-slim
    WORKDIR /usr/src/app
    COPY ./package.json ./
    COPY ./yarn.lock ./
    RUN yarn install
    COPY . .
    EXPOSE 5500
    CMD [ "index.js" ] 
    ```
    <b>Explanation</b>
    1. **Base Image**: Utilizes Node.js version 14 slim image.
    2. **Working Directory**: Sets the working directory inside the container to `/usr/src/app`.
    3. **Copy Dependencies**: Copies `package.json` and `yarn.lock` files from the host's current directory to the container's working directory.
    4. **Install Dependencies**: Executes `yarn install` inside the container to install dependencies specified in `package.json`, ensuring consistency using `yarn.lock`.
    5. **Copy Application**: Copies the entire contents of the host's current directory (including application source code) into the container's working directory.
    6. **Expose Port**: Exposes port 5500 on the container, indicating that the application inside the container will listen on this port.
    7. **Default Command**: Specifies `index.js` as the command to run when the container starts. This assumes that `index.js` is the entry point of the application and will be executed using Node.js.

3. ``` docker-compose.yml ``` in the root directory of the project.
    ```yaml
    version: "3"
    services:
    react-app:
        image: react-app
        stdin_open: true
        ports: 
        - "3000:3000"
        networks:
        - mern-app
    api-server:
        image: api-server
        ports:
        - "5500:5500"
        networks:
        - mern-app
        depends_on:
        - mongo
    mongo:
        image: mongo
        ports:
        - "27017:27017"
        networks:
        - mern-app
        volumes:
        - mongo-data:/data/db
    networks:
    mern-app:
        driver: bridge
    volumes:
    mongo-data:
        driver: local
    ```
    <b>Explanation</b>
  - **version**: Specifies the version of Docker Compose being used (version 3 in this case).
  
  - **services**: Defines individual containers/services in the application.
  
    - **react-app**: Defines a service named `react-app`. It uses an image named `react-app`, exposes port 3000, and connects it to the `mern-app` network.
  
    - **api-server**: Defines a service named `api-server`. It uses an image named `api-server`, exposes port 5500, and connects it to the `mern-app` network. It also depends on the `mongo` service.
  
    - **mongo**: Defines a service named `mongo`. It uses the official MongoDB image, exposes port 27017, connects it to the `mern-app` network, and mounts a volume named `mongo-data` to persist data.
  
  - **networks**: Defines the network named `mern-app` with the `bridge` driver. This allows containers within the same network to communicate with each other.
  
  - **volumes**: Defines a volume named `mongo-data` with the `local` driver, used to persist MongoDB data outside of the container.
