# T4.-Circuit-Breaker-Pattern-
# 🔌 Circuit Breaker Pattern

> **Module:** Fault Tolerance Module
> **Task:** T4 - Circuit Breaker Pattern

---

# 📖 Table of Contents

* [Introduction](#-introduction)
* [Objective](#-objective)
* [Problem Statement](#-problem-statement)
* [Why Circuit Breaker is Important](#-why-circuit-breaker-is-important)
* [What is a Circuit Breaker?](#-what-is-a-circuit-breaker)
* [Working Principle](#-working-principle)
* [Architecture Overview](#-architecture-overview)
* [Circuit Breaker States](#-circuit-breaker-states)
* [State Transition Diagram](#-state-transition-diagram)
* [Complete Workflow](#-complete-workflow)
* [Real-World Example (Online Interview Platform)](#-real-world-example-online-interview-platform)
* [Failure Scenario Without Circuit Breaker](#-failure-scenario-without-circuit-breaker)
* [Failure Scenario With Circuit Breaker](#-failure-scenario-with-circuit-breaker)
* [Advantages](#-advantages)
* [Limitations](#-limitations)
* [Best Practices](#-best-practices)
* [Testing Scenarios](#-testing-scenarios)
* [Interview Questions](#-interview-questions)
* [Future Enhancements](#-future-enhancements)
* [References](#-references)

---

# 📚 Introduction

Modern applications rarely work alone.

Instead of one large application, today's systems are built using **Microservices** that communicate with multiple external services such as:

* Authentication Service
* AI APIs
* Payment Gateway
* Email Service
* Database
* Video Streaming Service
* Notification Service

Every external service introduces the possibility of failure.

If one service becomes unavailable and our application keeps sending requests to it, the entire application may become slow or completely unavailable.

To solve this problem, software engineers use the **Circuit Breaker Pattern**.

---

# 🎯 Objective

The objective of this task is to understand how the Circuit Breaker Pattern improves the reliability and availability of distributed applications.

The study focuses on:

* Understanding the Circuit Breaker architecture
* Learning Closed, Open, and Half-Open states
* Understanding state transitions
* Learning how cascading failures occur
* Studying real-world applications

---

# ❗ Problem Statement

Suppose our Online AI Interview Platform depends on Google's Gemini API for generating interview questions.

Every candidate clicks:

```
Generate Question
```

The backend sends a request to Gemini.

If Gemini becomes unavailable,

every request waits until timeout.

If 5000 candidates use the platform simultaneously,

the server becomes overloaded.

Eventually,

even Login and Dashboard stop working.

This situation is called

# Cascading Failure

---

# 💡 Why Circuit Breaker is Important

A Circuit Breaker prevents repeated communication with a failing service.

Instead of waiting for every request to fail,

it temporarily blocks requests,

allowing the external service time to recover.

Benefits include:

* Prevents cascading failures
* Protects server resources
* Improves response time
* Provides graceful degradation
* Increases system reliability

---

# ⚡ What is a Circuit Breaker?

A Circuit Breaker is a **Fault Tolerance Design Pattern** that monitors failures in external services and automatically stops sending requests when failures exceed a predefined threshold.

Instead of continuously retrying failed operations,

it temporarily rejects requests and returns a fallback response.

---

# ⚙️ Working Principle

The Circuit Breaker continuously monitors:

* Failed Requests
* Successful Requests
* Timeouts
* Exceptions

When failures exceed a threshold,

the breaker opens.

After a recovery timeout,

it allows a limited number of requests.

If those requests succeed,

normal operation resumes.

---

# 🏗️ Architecture Overview

```
                        Candidate

                            │

                            ▼

                 Online Interview Platform

                            │

         ┌──────────────────┼──────────────────┐

         ▼                  ▼                  ▼

 Authentication        AI Question        Database

    Service             Generator

         │                  │

         ▼                  ▼

      Clerk API        Gemini API

```

The Circuit Breaker is placed between the Interview Service and external APIs.

---

# 🔄 Circuit Breaker States

The Circuit Breaker has **three states**.

## 1️⃣ Closed State

This is the normal operating state.

All requests are forwarded to the external service.

```
Candidate

↓

Backend

↓

Gemini API

↓

Question Generated
```

The breaker simply monitors failures.

Example

```
Request 1 ✅

Request 2 ✅

Request 3 ✅

Request 4 ✅
```

Everything is healthy.

---

## 2️⃣ Open State

When failures exceed the configured threshold,

the breaker enters the Open state.

Now,

no requests reach the external service.

Instead,

the application immediately returns

```
Service Temporarily Unavailable
```

Example

```
Candidate

↓

Circuit Breaker

↓

Fallback Response
```

No waiting.

No timeout.

No retries.

---

## 3️⃣ Half-Open State

After a recovery timeout,

the breaker enters Half-Open.

Only a few requests are allowed.

Example

```
Allowed Requests = 5
```

If all succeed,

```
Half Open

↓

Closed
```

If even one fails,

```
Half Open

↓

Open
```

---

# 📊 State Transition Diagram

```text
                +----------------------+
                |                      |
                |       CLOSED         |
                |                      |
                +----------------------+
                           |
             Failure Threshold Reached
                           |
                           ▼
                +----------------------+
                |                      |
                |        OPEN          |
                |                      |
                +----------------------+
                           |
                 Recovery Timeout Ends
                           |
                           ▼
                +----------------------+
                |                      |
                |     HALF-OPEN        |
                |                      |
                +----------------------+
                   |               |
             Success|               |Failure
                   ▼               ▼
              CLOSED            OPEN
```

---

# 🔁 Complete Workflow

```
User Requests Interview Question

↓

Interview Service

↓

Circuit Breaker

↓

Is Circuit Closed?

↓

YES

↓

Forward Request to Gemini

↓

Success?

↓

YES

↓

Return Question

↓

NO

↓

Increase Failure Count

↓

Failure Threshold Reached?

↓

YES

↓

Circuit Opens

↓

Return Fallback Message

↓

Wait Recovery Timeout

↓

Half Open

↓

Allow Limited Requests

↓

Successful?

↓

YES → Closed

NO → Open
```

---

# 🌍 Real-World Example (Online Interview Platform)

Imagine an AI-powered Interview Platform.

Services used:

* AI Question Generator
* Resume Analyzer
* Authentication
* Database
* Email Notification
* Video Interview API
* Speech-to-Text API

## Candidate Workflow

```
Login

↓

Choose Interview

↓

Generate AI Questions

↓

Submit Answers

↓

Speech-to-Text

↓

AI Evaluation

↓

Feedback Report
```

Every step may depend on external services.

The Circuit Breaker protects all of them.

---

# ❌ Failure Scenario Without Circuit Breaker

Suppose Gemini API becomes unavailable.

```
1000 Users

↓

Generate Question

↓

Gemini API

↓

Timeout

↓

Retry

↓

Timeout

↓

Retry

↓

Timeout

↓

CPU Usage ↑

Memory Usage ↑

Threads Blocked

↓

Server Crash
```

---

# ✅ Failure Scenario With Circuit Breaker

```
1000 Users

↓

Generate Question

↓

Circuit Breaker

↓

Open

↓

Instant Fallback Response

↓

Server Remains Healthy
```

Example fallback response

```json
{
  "status":"Service Unavailable",
  "message":"AI Question Service is temporarily unavailable.",
  "retry_after":"30 seconds"
}
```

---

# 🚀 Advantages

* Prevents cascading failures
* Improves fault tolerance
* Reduces server load
* Protects external services
* Faster failure response
* Better user experience
* Easy to monitor
* Supports graceful degradation
* Increases system stability
* Widely used in Microservices

---

# ⚠️ Limitations

* Incorrect threshold values may open the breaker too early.
* Requires monitoring and tuning.
* Fallback responses may offer limited functionality.
* Does not fix the underlying service failure.
* Adds implementation complexity.

---

# ✅ Best Practices

* Use timeout values for every external request.
* Configure reasonable failure thresholds.
* Log all state transitions.
* Monitor failure metrics.
* Provide meaningful fallback responses.
* Combine with Retry Pattern and Timeout Pattern.
* Test recovery scenarios regularly.

---

# 🧪 Testing Scenarios

| Test Case                 | Expected Result           |
| ------------------------- | ------------------------- |
| API responds successfully | Circuit remains Closed    |
| 5 consecutive failures    | Circuit changes to Open   |
| Recovery timeout expires  | Circuit enters Half-Open  |
| Trial requests succeed    | Circuit returns to Closed |
| Trial requests fail       | Circuit returns to Open   |

---

# 📈 Real-World Applications

Circuit Breakers are widely used in:

* Netflix
* Amazon
* Uber
* Microsoft Azure
* Google Cloud
* Banking Systems
* E-commerce Platforms
* Payment Gateways
* Healthcare Systems
* AI-based Applications

---

# 💼 Interview Questions

### What is a Circuit Breaker?

A fault tolerance pattern that stops requests to failing services after a threshold of failures.

---

### Why is it needed?

To prevent cascading failures and protect application resources.

---

### What are the three states?

* Closed
* Open
* Half-Open

---

### What happens in the Open state?

Requests are blocked immediately and a fallback response is returned.

---

### Why is Half-Open required?

To safely test whether the external service has recovered before restoring normal traffic.

---

### What is Cascading Failure?

When failure in one service causes failures across multiple dependent services.

---

# 🔮 Future Enhancements

This documentation can be extended by implementing Circuit Breakers using:

* Resilience4j (Java)
* Hystrix (Legacy)
* Polly (.NET)
* PyBreaker (Python)
* Spring Cloud Circuit Breaker

Additional features can include:

* Retry Pattern
* Timeout Pattern
* Bulkhead Pattern
* Health Monitoring
* Dashboard Metrics
* Alerting System

---

# 📌 Conclusion

The Circuit Breaker Pattern is a critical fault tolerance mechanism used in modern distributed systems. It prevents cascading failures by monitoring service health and controlling request flow based on service availability.

In an Online AI Interview Platform, it protects services such as AI Question Generation, Authentication, Speech-to-Text, Email Notifications, and Payment Gateways. By transitioning through **Closed → Open → Half-Open** states, the application remains responsive even when external dependencies fail.

Understanding this pattern is essential for building scalable, resilient, and production-ready microservice applications.

---

# 📚 References

1. Martin Fowler – Circuit Breaker Pattern
2. Microsoft Azure Architecture Center
3. Resilience4j Documentation
4. Netflix Hystrix Documentation
5. Microservices Patterns by Chris Richardson
6. Spring Cloud Circuit Breaker Documentation
7. Google Cloud Architecture Framework
8. AWS Well-Architected Framework

---

