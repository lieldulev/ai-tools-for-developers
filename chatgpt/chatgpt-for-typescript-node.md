# Generating Code Snippets in TypeScript and Node.js with Chat GPT
March 2023 / [@lieldulev](https://github.com/lieldulev "Liel Dulev") and [ChatGPT](https://chat.openai.com/chat)

## What is going on?
In recent years, artificial intelligence has made significant progress in various fields, including natural language processing. OpenAI's GPT-3 model is a prime example of this progress, capable of generating human-like language in various contexts, including code. Chat GPT is a large language model based on the GPT-3.5 architecture, trained to understand and generate code snippets in TypeScript and Node.js for various use cases, such as circuit breaker, NATS consumers, and K8s definitions using Terraform.

## Overview of Chat GPT and its Capabilities
Chat GPT is a text-based interface that allows developers to input natural language prompts and receive code snippets as responses. The model is trained on a vast corpus of TypeScript and Node.js code, which enables it to understand the semantics of programming concepts and generate relevant code snippets. The responses from Chat GPT are not hardcoded but generated on the fly, based on the input prompt and the model's understanding of the context.

Chat GPT can generate code snippets for various use cases, including, for example:

- Circuit Breaker: A design pattern used to prevent cascading failures in distributed systems.
- NATS Consumers: A type of message queue used in microservices architecture to handle asynchronous communication.
- K8s Definitions using Terraform: A way to automate the deployment of Kubernetes resources using Terraform infrastructure as code.
- Benefits of Using Chat GPT for Circuit Breaker, NATS Consumers, and K8s Definitions using Terraform

One of the primary benefits of using Chat GPT for generating code snippets is the reduction in development time and effort. Developers can input natural language prompts instead of writing code from scratch, saving time on syntax and semantics. Additionally, Chat GPT can generate code snippets that adhere to best practices, reducing the likelihood of errors and security vulnerabilities.

Another benefit of using Chat GPT is the ability to generate code snippets that fit specific use cases and requirements. Developers can input prompts with specific use case scenarios or constraints, and Chat GPT can generate code snippets that meet those requirements.

## How to use Chat GPT for writing Typescript / NodeJS?

Type this into the chat:

```
You are Code GPT, are typescript and NodeJS coding assistant,
when i ask you to write code for me provide both the code and questions to help refine my next code request
```

This will trigger Chat GPT with a response and will make the iterations on the code easier. 

## Examples of Chat GPT Prompts and Responses
Let's look at some examples of Chat GPT prompts and the corresponding responses for circuit breaker, NATS consumers, and K8s definitions using Terraform.

### Websockets and Redis
Prompt:
```
Write an http server that can handle a large pool of websockets connection
and routes the message from and to redis pubsub.
```

Response:
```typescript
import http from 'http';
import WebSocket from 'ws';
import Redis from 'ioredis';

const redis = new Redis();

// Create an HTTP server
const server = http.createServer();

// Create a WebSocket server
const wss = new WebSocket.Server({ server });

// Listen for WebSocket connections
wss.on('connection', (ws: WebSocket) => {
  console.log('WebSocket connection established');

  // Listen for WebSocket messages
  ws.on('message', async (message: string) => {
    console.log(`Received message: ${message}`);

    // Publish the message to Redis Pub/Sub
    await redis.publish('websocket', message);
  });
});

// Subscribe to Redis Pub/Sub
redis.subscribe('websocket');

// Listen for Redis Pub/Sub messages
redis.on('message', (channel: string, message: string) => {
  console.log(`Received Redis message: ${message}`);

  // Send the message to all connected WebSocket clients
  wss.clients.forEach((client) => {
    if (client.readyState === WebSocket.OPEN) {
      client.send(message);
    }
  });
});

// Start the HTTP server
server.listen(8080, () => {
  console.log('Server is listening on port 8080');
});
```
After the code above Chat GPT also asked the following:

```text
Here are some follow-up questions to help refine your next code request:

1. Is there any specific protocol or message format that you want to use for WebSocket messages?
2. Do you want to add any authentication or authorization for the WebSocket connections?
3. Are there any additional requirements for the Redis Pub/Sub, such as filtering or rate limiting messages?
4. Do you want to add any error handling or logging for the HTTP server or WebSocket connections?
5. Is there anything else you'd like me to add or modify in this code snippet?
```

Use those question to refine your next request for code by being more specific.

### Circuit Breaker
Prompt:
```
Create a circuit breaker pattern for a microservice that communicates with a database.
The microservice should fail gracefully and retry when the database is temporarily unavailable.
The circuit should open when the database is unavailable for more than 10 seconds.
```

Response:
```typescript
import circuitBreaker from 'opossum';
import { DatabaseService } from './database.service';

const options = {
  errorThresholdPercentage: 50,
  resetTimeout: 10000,
  timeout: 5000,
};

const breaker = circuitBreaker(DatabaseService.find, options);

breaker.fallback(() => {
  // handle fallback logic
});

export async function find(query: any): Promise<any> {
  return await breaker.fire(query);
}
```

### NATS Consumers
Prompt:
```text
Create a NATS consumer that subscribes to a message queue and processes messages asynchronously.
The consumer should handle errors gracefully and retry failed messages up to three times.
```

Response:
```typescript
import { connect } from 'nats';
import { Message } from 'node-nats-streaming';

const nc = connect({ servers: ['nats://localhost:4222'] });

const options = {
  maxReconnectAttempts: 10,
  waitOnFirstConnect: true,
};

const consumer = nc.subscribe('queue', { durable: true }, (message: Message) => {
  try {
    // process message here
  } catch (error) {
    if (message.getRedeliveredCount() < 3) {
      message.nak();
    } else {
      // handle fallback logic
    }
  }
});

consumer.on('error', (error: Error) => {
  // handle error logic here
});
```

### K8s Definitions using Terraform

Prompt:

```text
Create a Kubernetes deployment using Terraform infrastructure as code.
The deployment should have one replica, use the nginx image, and expose port 80.
```

Response:
```terraform
resource "kubernetes_deployment" "nginx" {
  metadata {
    name = "nginx"
  }

  spec {
    replicas = 1

    selector {
      match_labels = {
        app = "nginx"
      }
    }

    template {
      metadata {
        labels = {
          app = "nginx"
        }
      }

      spec {
        container {
          image = "nginx:latest"
          name  = "nginx"
          ports {
            container_port = 80
          }
        }
      }
    }
  }
}

resource "kubernetes_service" "nginx" {
  metadata {
    name = "nginx"
  }

  spec {
    selector = {
      app = "nginx"
    }

    port {
      port        = 80
      target_port = 80
    }

    type = "ClusterIP"
  }
}
```

## Integrating and Modifying Generated Code Snippets
Integrating the generated code snippets into TypeScript and Node.js projects is relatively straightforward. Developers can copy and paste the code snippets into their projects and modify them as needed to fit specific requirements. For instance, in the case of the K8s deployment example, developers may want to modify the image used or change the exposed port.

One thing to note is that the generated code snippets are not guaranteed to be optimal or the best fit for every scenario. Therefore, developers should review and test the generated code snippets thoroughly before using them in production.

Potential for Improving Development Productivity and Code Quality
Chat GPT has the potential to improve development productivity and code quality significantly. By generating code snippets, developers can save time and effort on syntax and semantics, enabling them to focus on the business logic of their applications. Additionally, the generated code snippets can adhere to best practices, reducing the likelihood of errors and security vulnerabilities.

However, there are some limitations to Chat GPT that developers should be aware of. The model is not perfect and can generate code snippets that are not optimal or the best fit for specific scenarios. Additionally, the model can generate code snippets that may not adhere to company or team coding standards, requiring additional modification.

## Conclusion
Chat GPT is a powerful tool that can generate code snippets in TypeScript and Node.js for various use cases, including circuit breaker, NATS consumers, and K8s definitions using Terraform. The model is trained on a vast corpus of TypeScript and Node.js code, enabling it to understand programming concepts and generate relevant code snippets.

Using Chat GPT for generating code snippets can save time and effort on syntax and semantics, enable developers to focus on the business logic of their applications, and generate code snippets that adhere to best practices. However, developers should review and test the generated code snippets thoroughly before using them in production and be aware of the model's limitations.
