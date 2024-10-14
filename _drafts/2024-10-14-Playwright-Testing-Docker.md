---
title: Playwright E2E Testing in Docker
categories: [E2E Testing, Playwright]
tags: [e2e-testing, playwright, docker]
---

[Playwright](https://playwright.dev) seems to be the tool of choice for end-to-end testing at the moment. It's been used quite extensively on recent projects that I've been involved in. In this post, I'm going to show how you can use Docker and Docker Compose to run your tests locally and in your CI pipeline with a Node.js application.

## Default Docker Image

If you go to the [docs](https://playwright.dev/docs/docker), you'll see the option to use an image which includes everything that you need to run your tests (if you're testing on all browsers of course). Pull the image (I'm using the `jammy` image here):

```bash
docker pull mcr.microsoft.com/playwright:v1.48.0-jammy
```

As of this post, it's showing as being **2.03GB** in size. What if we only wanted to run tests on the Chromium browser? This is where building your own image comes in.

## Building Your Own Image

Here's an example Dockerfile that you can use to install chromium only:

```docker
# syntax=docker/dockerfile:1

FROM node:20-slim

# Set the working directory inside the container.
WORKDIR /app

# Copy package.json and package-lock.json to the container.
COPY package*.json ./

# Clear npm cache and install dependencies
RUN npm cache clean --force && \
    npm install && \
    npx -y playwright@1.48.0 install --with-deps chromium

# Set the environment path to node_modules/.bin
ENV PATH=/app/node_modules/.bin:$PATH

# Copy the rest of your application code into the container.
COPY . .

# Expose the application port
EXPOSE 5173

# Set the entrypoint to run the commands
ENTRYPOINT ["sh", "-c", "sleep 10 && npm run test"]
```

With this approach, we've managed to get the size down to **1.31GB**. It's a huge improvement, and I'm hoping to get the image size down further.
