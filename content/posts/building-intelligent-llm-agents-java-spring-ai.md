---
title: "Beyond ChatGPT: Building Intelligent LLM Agents and Tool-Using Applications in Java with Spring AI"
date: 2025-08-05T10:00:00+05:30 # Set to a slightly future date for immediate visibility
draft: false
description: "Unlock the true potential of LLMs in Java! Learn to build intelligent AI agents that use tools (function calling) to interact with external systems and solve complex problems with Spring AI."
images:
  - "images/llm-agents-java-spring-ai-banner.jpg" # Consider creating this banner image
tags: ["Java", "AI", "Generative AI", "LLM Agents", "Tool Use", "Function Calling", "Spring AI", "Prompt Engineering", "Autonomous AI"]
categories: ["AI", "Java Development", "Spring Boot"]
author: "JavaYou.com Team"
---

# Beyond ChatGPT: Building Intelligent LLM Agents and Tool-Using Applications in Java with Spring AI

Welcome back, fellow Java innovators! In our last guide, we explored the foundational power of Generative AI and how Spring AI makes it incredibly easy to integrate Large Language Models (LLMs) into your Java applications. We touched on generating text, summarization, and basic Q&A. But what if you want your LLM to do more than just generate text? What if you need it to fetch real-time data, interact with your internal APIs, or perform complex, multi-step actions?

This is where the true magic of **LLM Agents** and **Tool Use (Function Calling)** comes into play. No longer are LLMs just "chatbots"; they're becoming intelligent components capable of reasoning, planning, and executing actions in the real world. And the best part? Spring AI provides robust, developer-friendly features to bring this cutting-edge capability right into your familiar Java ecosystem.

In this deep dive, we'll go beyond basic conversational AI. We'll unravel the concepts of LLM agents and tools, demonstrate how Spring AI empowers your Java applications to leverage these features, and walk through practical examples to build truly intelligent, action-oriented systems. Get ready to transform your Java applications from reactive responses to proactive intelligence!

## 1. The Next Frontier: LLM Agents and Tool Use

To truly unlock the power of LLMs in enterprise applications, we need them to move beyond simple text generation. They need to become active participants, capable of interacting with the world around them.

### 1.1 What are LLM Agents?

An **LLM Agent** is an LLM that is equipped with:
1.  **Reasoning Capabilities:** The ability to understand a goal, break it down into steps, and determine what actions are needed.
2.  **Memory:** The ability to remember past interactions or context (short-term and long-term).
3.  **Tools:** Access to external functions or APIs that allow it to interact with the real world (e.g., search the web, query a database, send an email, call a weather API).
4.  **Observation & Iteration:** The ability to observe the results of its actions and iterate on its plan if needed.

Essentially, an LLM agent uses an LLM as its "brain" to decide *what to do next*, often by selecting and using one of its available "tools."

### 1.2 What is Tool Use (Function Calling)?

**Tool Use**, often referred to as **Function Calling** (especially by OpenAI and Google), is the mechanism that allows an LLM to decide when and how to invoke external functions. Instead of just generating a text response, the LLM generates a structured output (e.g., JSON) that specifies:
* Which tool/function to call.
* What arguments to pass to that tool.

Your application then intercepts this output, executes the specified tool, and optionally feeds the tool's result back to the LLM for further reasoning or response generation. This creates powerful feedback loops.

**Why is this a game-changer for Java?**
* **Connecting LLMs to Business Logic:** LLMs can now directly interact with your existing microservices, databases, and APIs.
* **Real-time Data:** Inject dynamic, up-to-date information into LLM responses.
* **Automated Workflows:** Build complex workflows where the LLM orchestrates actions.
* **Reduced Hallucination:** By providing the LLM with direct access to facts via tools (like a search engine or internal knowledge base), you significantly reduce its tendency to "hallucinate" incorrect information.

## 2. Spring AI's Elegant Approach to Tools

Spring AI makes integrating tools with LLMs remarkably straightforward, leveraging familiar Spring idioms. It handles the low-level API calls to tell the LLM about your available functions and parse the LLM's tool call requests.

### 2.1 The `FunctionCallback` and `FunctionCallbackWrapper`

Spring AI introduces the concept of `FunctionCallback` (or more commonly, `FunctionCallbackWrapper` for simpler use) to register your Java methods as callable "tools" for the LLM.

**Key steps in Spring AI Tool Use:**
1.  **Define your Tool/Function:** Create a simple Java method that performs a specific task (e.g., `getWeather`, `searchProduct`).
2.  **Describe the Tool:** Provide a clear description of what the tool does and its parameters. This description is sent to the LLM so it knows when and how to use it.
3.  **Register the Tool:** Register this function with Spring AI's `ChatClient`.
4.  **Invoke the LLM:** Make a request to the `ChatClient`. If the LLM determines a tool is needed, it will generate a tool call.
5.  **Execute the Tool:** Your application receives the tool call from Spring AI, executes the actual Java method, and then optionally sends the result back to the LLM.

## 3. Practical Example: Building a Weather Assistant with Tools

Let's build a Spring Boot application that enables an LLM to fetch real-time weather information using a tool.

**Prerequisites:**
* JDK 17 or higher
* Maven or Gradle
* An IDE (IntelliJ IDEA)
* An API Key for an LLM provider (e.g., OpenAI or Google Gemini).
* *Optional:* An API Key for a weather service (e.g., OpenWeatherMap, if you were to build a real external API call). For simplicity, we'll use a dummy weather service in our example.

### 3.1 Project Setup

1.  Go to [start.spring.io](https://start.spring.io/)
2.  **Project:** Maven Project
3.  **Language:** Java
4.  **Spring Boot:** 3.x.x (latest stable)
5.  **Java:** 17
6.  **Dependencies:**
    * `Spring Web`
    * `Spring AI OpenAI Chat Starter` (or `Spring AI Google Gemini Chat Starter`)
7.  Click "Generate" and download, then open in your IDE.

### 3.2 Configure API Keys

Add your LLM API key to `src/main/resources/application.properties`:

```properties
spring.ai.openai.api-key=<YOUR_OPENAI_API_KEY>
# Or for Gemini:
# spring.ai.google.gemini.api-key=<YOUR_GOOGLE_GEMINI_API_KEY>
3.3 Define Your Weather Tool
First, let's create a simple service that simulates fetching weather data. In a real application, this would call an external weather API.

Create src/main/java/com/example/aichatbot/WeatherService.java:

Java

package com.example.aichatbot;

import org.springframework.stereotype.Service;

// Define a record for the weather response, mapping to tool's output schema
record WeatherResponse(String city, String temperature, String conditions, String unit) {}

@Service
public class WeatherService {

    /**
     * Retrieves the current weather for a given city.
     * This method acts as a "tool" for the LLM.
     * @param city The city name for which to get the weather.
     * @param unit The unit of temperature (e.g., "celsius" or "fahrenheit").
     * @return Weather information in a structured format.
     */
    public WeatherResponse getCurrentWeather(String city, String unit) {
        // In a real app, this would call an external weather API
        // For demonstration, we'll return mock data based on city/unit
        System.out.println("DEBUG: Calling WeatherService for city: " + city + ", unit: " + unit); // Log tool call

        if ("london".equalsIgnoreCase(city) && "celsius".equalsIgnoreCase(unit)) {
            return new WeatherResponse(city, "15°C", "Cloudy", "celsius");
        } else if ("london".equalsIgnoreCase(city) && "fahrenheit".equalsIgnoreCase(unit)) {
            return new WeatherResponse(city, "59°F", "Cloudy", "fahrenheit");
        } else if ("new york".equalsIgnoreCase(city) && "fahrenheit".equalsIgnoreCase(unit)) {
            return new WeatherResponse(city, "72°F", "Sunny", "fahrenheit");
        } else if ("new york".equalsIgnoreCase(city) && "celsius".equalsIgnoreCase(unit)) {
            return new WeatherResponse(city, "22°C", "Sunny", "celsius");
        }
        return new WeatherResponse(city, "N/A", "Unknown", unit);
    }
}
3.4 Create Your AI Controller with Tool Registration
Now, let's integrate this WeatherService as a tool with our ChatClient.

Create src/main/java/com/example/aichatbot/AiToolController.java:

Java

package com.example.aichatbot;

import org.springframework.ai.chat.client.ChatClient;
import org.springframework.ai.chat.client.Request;
import org.springframework.ai.chat.model.ChatResponse;
import org.springframework.ai.chat.prompt.Prompt;
import org.springframework.ai.model.function.FunctionCallback;
import org.springframework.ai.model.function.FunctionCallbackWrapper;
import org.springframework.context.annotation.Bean;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;
import java.util.function.Function;

@RestController
public class AiToolController {

    private final ChatClient chatClient;

    // Inject the WeatherService
    private final WeatherService weatherService;

    // The FunctionCallbackWrapper requires a Function. We'll define a record for input.
    // This record's fields define the parameters the LLM can use for the tool.
    record WeatherFunctionInput(String city, String unit) {}

    public AiToolController(ChatClient.Builder chatClientBuilder, WeatherService weatherService) {
        this.weatherService = weatherService;

        // 1. Define the FunctionCallbackWrapper for our tool
        FunctionCallback weatherFunction = FunctionCallbackWrapper.builder(
                "getCurrentWeather", // This is the name the LLM will see and try to call
                "Get the current weather for a specified city.", // Description for the LLM
                (json) -> { // Lambda that processes the JSON arguments from the LLM
                    // Convert JSON arguments into our input record
                    WeatherFunctionInput input = new WeatherFunctionInput(
                            (String) json.get("city"),
                            (String) json.get("unit"));
                    // Call our actual Java service method with the extracted arguments
                    return weatherService.getCurrentWeather(input.city(), input.unit());
                })
                .with  (new Function<WeatherFunctionInput, WeatherResponse>() {
                    @Override
                    public WeatherResponse apply(WeatherFunctionInput weatherFunctionInput) {
                        return weatherService.getCurrentWeather(weatherFunctionInput.city(), weatherFunctionInput.unit());
                    }
                })
                .build();


        // 2. Build the ChatClient and register the tool
        this.chatClient = chatClientBuilder
                .functions(weatherFunction) // Register our tool with the ChatClient
                .build();
    }

    @GetMapping("/weather-chat")
    public String chatWithWeatherTool(@RequestParam(value = "message", defaultValue = "What's the weather like in London?") String message) {
        ChatResponse response = chatClient.prompt()
                .user(message)
                .call()
                .chatResponse(); // Get the full chat response object

        // Check if the LLM decided to call a tool
        if (response.getResult().getOutput().getFunctionCall() != null) {
            // In a real scenario, you'd handle the tool call, execute the function,
            // and then potentially send the result back to the LLM.
            // For now, let's just indicate that a tool was called.
            return "The AI decided to call the 'getCurrentWeather' tool. " +
                   "Output: " + response.getResult().getOutput().getContent();
        } else {
            return response.getResult().getOutput().getContent();
        }
    }

    @GetMapping("/weather-agent")
    public String weatherAgent(@RequestParam(value = "query", defaultValue = "What is the temperature in New York in Celsius?") String query) {
        // This demonstrates a more "agent-like" flow where the LLM can use the tool
        // and then provide a human-readable answer.

        // The chatClient is configured with the tool.
        // We prompt the LLM. Spring AI will handle the function calling mechanism.
        // If the LLM decides to call the 'getCurrentWeather' function,
        // our WeatherService will be invoked and its result fed back to the LLM.
        return chatClient.prompt()
                .user(query)
                .call()
                .content(); // This directly gets the final content from the LLM, after tool use
    }
}
A note on the FunctionCallbackWrapper: The FunctionCallbackWrapper setup is where you define the name of your tool, its description, and the lambda that extracts arguments from the JSON provided by the LLM and calls your actual Java method (weatherService.getCurrentWeather).

3.5 Run and Test Your Agent
Run your Spring Boot application (e.g., from your IDE or ./mvnw spring-boot:run).

Open your web browser or use Postman/curl:

http://localhost:8080/weather-chat?message=What is the weather like in London right now?

Expected behavior: The LLM should recognize it needs weather data and try to invoke the getCurrentWeather tool. You should see DEBUG: Calling WeatherService... in your application logs. The response in your browser will indicate the tool was called.

http://localhost:8080/weather-agent?query=What is the temperature in New York in Celsius?

Expected behavior: The LLM should understand the query, call the getCurrentWeather tool (you'll see the DEBUG log), and then use the result to provide a natural language answer like "The temperature in New York is 22°C, and it's Sunny."

http://localhost:8080/weather-chat?message=Tell me a joke.

Expected behavior: The LLM will not call the weather tool, as it's not relevant. It will just tell you a joke directly.

This demonstrates how Spring AI bridges the gap, allowing your LLM to intelligently decide when and how to interact with your Java business logic.

4. Building More Sophisticated LLM Agents
The weather example is a starting point. Real-world LLM agents are more complex, involving multiple tools and a sophisticated decision-making process.

4.1 Orchestrating Multiple Tools
Your agent can have access to many tools: a product search API, an order placement API, an email sender, a knowledge base lookup, etc. The LLM's role is to decide which tool (or sequence of tools) to use to achieve the user's goal.

Spring AI allows you to register multiple FunctionCallbackWrapper instances with your ChatClient.Builder.

Java

// Example: Adding another tool
FunctionCallback sendEmailFunction = FunctionCallbackWrapper.builder(
        "sendEmail",
        "Send an email to a recipient with a subject and body.",
        (json) -> { /* logic to send email */ })
        .build();

this.chatClient = chatClientBuilder
        .functions(weatherFunction, sendEmailFunction) // Register multiple tools
        .build();
4.2 Handling Complex Agentic Workflows
For truly complex tasks, a simple single-turn tool call might not suffice. LLM agents often involve:

Planning: The LLM internally outlines a plan (e.g., "First, search for product. If found, add to cart. Then, confirm order.").

Execution: Executing steps based on the plan, possibly calling multiple tools sequentially.

Observation: The agent observes the results of tool calls.

Re-planning/Iteration: If a tool call fails or doesn't yield expected results, the agent can re-plan or ask for clarification.

While Spring AI provides the core tool-calling mechanism, building a full-fledged autonomous agent often involves combining it with:

State Management: Storing conversation history and intermediate steps.

Feedback Loops: Feeding tool output back to the LLM prompt.

Error Handling: Gracefully managing failed tool calls.

4.3 Agent Frameworks (Conceptual)
More advanced agentic behaviors might leverage higher-level frameworks (which Spring AI is continuously evolving to support):

Chains: A predefined sequence of LLM calls and tool uses.

Agents: The LLM decides the sequence and tools dynamically.

5. Agent Design Principles for Enterprise Java
When building LLM agents in production Java environments, consider:

Security: Ensure tool functions have appropriate access control. LLMs should only be able to invoke functions they are authorized for.

Auditability: Log all LLM inputs, tool calls, and outputs for debugging, compliance, and monitoring.

Idempotency: Design your tool functions to be idempotent where possible, meaning repeated calls have the same effect, to gracefully handle retries.

Latency & Performance: LLM API calls and tool executions can introduce latency. Design for asynchronous operations where appropriate (e.g., using Spring WebFlux or Java's Virtual Threads for non-blocking I/O).

Cost Optimization: Monitor token usage. Design prompts and tools efficiently to minimize unnecessary LLM calls.

6. The Future of Intelligent Java Applications
The ability to build LLM agents and use tools is a pivotal moment for Java development. It transforms LLMs from mere content generators into powerful, interactive components within your existing enterprise architectures.

Imagine:

An internal HR assistant that can retrieve employee data, update records, and draft emails based on natural language commands.

A customer support agent that can check order status, initiate refunds, and troubleshoot issues by integrating with your backend systems.

A financial analyst tool that queries real-time market data, performs calculations, and generates detailed reports, all orchestrated by an LLM.

Spring AI is rapidly evolving to support even more sophisticated agentic workflows, paving the way for a new generation of intelligent, automated, and hyper-responsive Java applications. The blend of Java's enterprise readiness with AI's intelligence is truly powerful.

Are you ready to build the next generation of intelligent, tool-using Java applications? Share your insights in the comments below!

