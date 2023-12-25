# About this project
    When we need to make changes in the local folder and map it to the container folder volumes concepts come in handy
    docker-compose is using non- default docker file (Dockerfile.dev) this config is done under build command. 

## map volumes in command
    docker run -it -p 3000:3000 -v /home/node/app/node_modules -v ~/exercises/frontend:/home/node/app iamyuvi:frontend

## explanation of command :
execute docker(docker run) 
- in provide input(-i) and format mode(-t) ,
- map the local machine port with the port running inside docker container (-p machineport:containerport)
- create reference from the continaer directory to local directoty (-v localmachinedirectory:containerdirectory)
- create a placholder or inform to the docker that do not reference this particular directory to any other directory.

    why do we create placeholder?

        - while we create references in local directory node_modules does not exists, in this case container node_modules directory could be deleted. so it is the way to tell docker explicitly do not touch this particular foler when performing reference.

    why do we need to create reference?
    
        - when we make some changes in the local folder we expect it to be reflected in the output served by docker container

## modify start command on running container
 docker exec -it b582f61c8680 npm run test - run existing container with different startup command passed through terminal


## When using WSL to run Docker Desktop on Windows, the project should have been created on the Linux file system and all docker commands should be run within WSL as per best practices:

https://docs.docker.com/desktop/windows/wsl/#best-practices

If the project was created on the Windows file system, the volumes may not work correctly and performance may greatly suffer. To address this, you will need to copy your project to the Linux File system using the following instructions:

1. To open your WSL operating system use the search / magnifying glass in the bottom system tray:


2. Type the name of your distribution (by default it is Ubuntu) and click Open:





3. When the terminal launches it should automatically open to the home directory on the Linux filesystem.

4. Run explorer.exe . to open up the file explorer at /home/USERNAME directory within WSL:




5. Move the frontend project directory into the WSL file browser window:


6. Your project path should now look like /home/USERNAME/frontend. Run ls to confirm that you are in the correct location. Then, run cd frontend to change into the project directory.


7. Delete any node_modules or package-lock.json files that may exist in the project directory. If these were generated on the Windows file system and were copied over, they will conflict.

8. Update your Dockerfile.dev to look like this:

FROM node:16-alpine
 
USER node
 
RUN mkdir -p /home/node/app
WORKDIR /home/node/app
 
COPY --chown=node:node ./package.json ./
RUN npm install
COPY --chown=node:node ./ ./
 
CMD ["npm", "start"]

Explanation of changes:

We are specifying that the USER which will execute RUN, CMD, or ENTRYPOINT instructions will be the node user, as opposed to root (default).

https://docs.docker.com/engine/reference/builder/#user

We are then creating a directory of /home/node/app prior to the WORKDIR instruction. This will prevent a permissions issue since WORKDIR by default will create a directory if it does not exist and set ownership to root.

The inline chown commands will set ownership of the files you are copying from your local environment to the node user in the container.

The end result is that some files and directories will no longer be owned by root, and no npm processes will be run by the root user. Instead, they will all be owned and run by the node user.

9. Using the WSL terminal build your Docker image as you typically would:
docker build -f Dockerfile.dev -t USERNAME:frontend .

10. Using the WSL terminal, start and run a container. It is very important that you do not use a PWD variable as shown in the lecture video. Use the ~ alias for the home directory or type out the full path:
Using ~ alias:
docker run -it -p 3000:3000 -v /home/node/app/node_modules -v ~/frontend:/home/node/app USERNAME:frontend

Using the full path:
docker run -it -p 3000:3000 -v /home/node/app/node_modules -v /home/YOURUSERNAME/frontend:/home/node/app USERNAME:frontend

11. Going forward in this course, all Docker commands and projects should be run within WSL and not Windows.

Changes needed for Docker Compose - Single Container project

When we refactor this project to use Docker Compose in a few lectures, remember to update the working directory paths in your compose file:

    volumes:
      - /home/node/app/node_modules
      - .:/home/node/app


Explanation of the issue:

The core of the issue was the volume bookmarking (placeholder) was created by root. As a result, the node_modules will be owned by root. As part of the development process, the Webpack / Babel Loader will attempt to create a node_modules.cache folder as the Node user. In Node 15, 16+, this will result in a EACCES: permission denied.

Adding WSL project folder to VSCode
Option #1
https://code.visualstudio.com/docs/remote/wsl#_open-a-remote-folder-or-workspace

Option #2
With the terminal of your distro, make sure you are inside your frontend project directory and run the explorer.exe . command. This will open a file browser window within the WSL location. Copy the location path:


Go to VSCode and select File, then Open Folder. Paste the wsl address you just copied into the file browser window and click the Select Folder button:


This will add the project that is located in the WSL file system into your VSCode workspace.
# Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in your browser.

The page will reload when you make changes.\
You may also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can't go back!**

If you aren't satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you're on your own.

You don't have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn't feel obligated to use this feature. However we understand that this tool wouldn't be useful if you couldn't customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

### Code Splitting

This section has moved here: [https://facebook.github.io/create-react-app/docs/code-splitting](https://facebook.github.io/create-react-app/docs/code-splitting)

### Analyzing the Bundle Size

This section has moved here: [https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size](https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size)

### Making a Progressive Web App

This section has moved here: [https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app](https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app)

### Advanced Configuration

This section has moved here: [https://facebook.github.io/create-react-app/docs/advanced-configuration](https://facebook.github.io/create-react-app/docs/advanced-configuration)

### Deployment

This section has moved here: [https://facebook.github.io/create-react-app/docs/deployment](https://facebook.github.io/create-react-app/docs/deployment)

### `npm run build` fails to minify

This section has moved here: [https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify](https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify)
