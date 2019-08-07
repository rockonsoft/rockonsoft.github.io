# Running a NestJS API on Google Cloud Run

- Google Cloud Run is Google's serverless container engine. Cloud Run lets you manage containers without the need to worry about infrastructure. You bring your Docker file and Cloud Run will build, deploy and manage your container. [More...](https://cloud.google.com/run/)
- NestJS is Angular on the server side. It has great tooling to quickly create typescript API code. It uses the same paradigm as Angular, so you can inject services into controllers to build well organized server side Typescript. [More..](https://nestjs.com/)

In this blog I will show how to quickly build a hosted container that serves a NestJS API.

I will not be going through the Google Cloud account creation and setup. Google has done a great job of documenting this. This blog assumes that you have a GCP (Google Cloud Platform) instance. If not, [start here](https://cloud.google.com/free/). It also assumes that you have created a project and have configured billing for that project. You also need to install the [GCP SDK and tools.](https://cloud.google.com/sdk/install)

## TLTR

### You Need

- Google Cloud Platform (GCP) account and instance.
- GCP project with billing enabled.
- GCP SDK and tools installed.

### Run Nest API on Cloud Run

- Create the Nest API with Nest CLI
- Add Dockerfile
- Set the port as specified in process.env
- Build and deploy with gcloud build / deploy

## Create a Nest API

The code for this section can be found [here](https://github.com/rockonsoft/nest-cloud-run) - use the 'rock/run-in-cloud' branch.

- Install the CLI tools (globally if needed)

```bash
$ npm i -g @nestjs/cli
```

- Create a new Nest project. (I called mine nest-cloud-run)

```bash
$ nest new nest-cloud-run
```

- After the project is generated, you can build and run the API locally. It has a default endpoint served at localhost:3000 and it will serve the required "Hello world" message.
  To test this, run the following and then browse to localhost:3000

```bash
$ npm run start:dev
```

## Containerize

In order to run the API in Cloud Run it needs to be containerized. Add the Dockerfile to the project. To build the container we leverage the Node 10 image, install Typescript, copy the project over and compile the Typescript. The API is then served with the npm command.

If you have docker installed locally you could build the image locally to check that it works. Before we can build and deploy the API to Cloud Run, we need to read the port from the Node environment variable. When hosted in Cloud Run, the port will be assigned dynamically and it is set as a environment variable. If you don't do this the image will fail to start.
In main.ts add:

```typescript
const port = process.env.PORT || 8080
await app.listen(port)
```

To give me a clear understanding of the Node environment used by the container, I decided to change the default app service, to output all the environment variables. This is not a good idea for production instances, as it will contain sensitive information. It is, however a great help for understanding the environment the API is running in. I changed the getHello function in app.service.ts to:

```typescript
  getHello(): string {
    return JSON.stringify(process.env);
  }
```

The API is now ready to be build and deployed in Cloud Run.

## Build and Deploy

To build the container in the Cloud Run, execute the following GCP cli command:

```bash
$ gcloud builds submit --tag gcr.io/[PROJECT-ID]/nest-cloud-run
```

This will TAR your source code, upload it to a Google Storage Bucket and build the image as specified in the Docker file. The bucket can be accessed through the GCP console. You can download the .TAR file to check what content was uploaded.
Once the build is done you would receive detail about the build and the image.

You can now deploy the image as a service in Cloud Run with the command:

```bash
$ gcloud beta run deploy --image gcr.io/[PROJECT-ID]/nest-cloud-run --platform managed
```

The command outputs the URL for the service.
The deployed service, the NestJS API can now be accessed by browsing to that URL. This should serve the default route, and if you made the changes to the app.service.ts, you should see the stringified PROCESS.ENV.

Now you have a deployed container running a Nest API. In a quick few steps you have created the starting point for serverless API, where you are in full control of the running environment through the Dockerfile.

Delete all resources once you are done as they will incur a cost.
