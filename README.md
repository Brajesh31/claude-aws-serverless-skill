# claude-aws-serverless-skill

A Claude Code skill for designing, implementing, deploying, and operating serverless applications on AWS.

## Overview

This skill focuses on event-driven application architectures built from managed cloud services. It helps developers design Lambda functions, API endpoints, asynchronous workflows, data access patterns, and deployment configurations while considering reliability, security, and operational cost.

## Main Service Areas

- AWS Lambda for event-driven compute.
- API Gateway for HTTP and API entry points.
- DynamoDB for managed NoSQL persistence.
- SQS for decoupled asynchronous processing.
- SNS for event distribution and notifications.
- AWS SAM and CDK for infrastructure definition and deployment.

## What the Skill Covers

### Lambda Development

- Handler structure and event parsing.
- Reuse of clients and configuration outside the handler.
- Validation and structured error handling.
- Idempotency and retry-safe processing.
- Cold-start and execution-time considerations.

### API Design

- Route and method organization.
- Request validation and response shaping.
- Authentication and authorization boundaries.
- Consistent error responses.
- Rate limits and operational safeguards.

### Event-Driven Workflows

- Queue-based processing with SQS.
- Fan-out and notifications with SNS.
- Retry and dead-letter queue strategies.
- Duplicate delivery and idempotency.
- Event schema and version management.

### Data Access

- DynamoDB key design.
- Efficient query patterns.
- Conditional writes and concurrency control.
- Access patterns that avoid unnecessary scans.
- Data retention and lifecycle considerations.

### Deployment and Operations

- Infrastructure as code with SAM or CDK.
- Environment-specific configuration.
- Least-privilege IAM permissions.
- Logs, metrics, alarms, and tracing.
- Rollback and deployment validation.

## Installation

Clone the repository into the Claude Code skills directory:

```bash
git clone https://github.com/Brajesh31/claude-aws-serverless-skill.git ~/.claude/skills/aws-serverless
```

## Usage

Invoke the skill for AWS serverless design or implementation tasks:

```text
/aws-serverless
```

Provide the workload, expected traffic, event sources, data model, security requirements, deployment method, and operational constraints.

## Example Lambda Handler

```javascript
const { DynamoDBClient } = require("@aws-sdk/client-dynamodb");
const {
  DynamoDBDocumentClient,
  GetCommand,
} = require("@aws-sdk/lib-dynamodb");

const client = new DynamoDBClient({});
const db = DynamoDBDocumentClient.from(client);

exports.handler = async (event) => {
  const id = event?.pathParameters?.id;

  if (!id) {
    return {
      statusCode: 400,
      body: JSON.stringify({ error: "Missing id" }),
    };
  }

  try {
    const result = await db.send(
      new GetCommand({
        TableName: process.env.TABLE_NAME,
        Key: { id },
      })
    );

    return {
      statusCode: 200,
      body: JSON.stringify(result.Item ?? null),
    };
  } catch (error) {
    console.error("Request failed", { name: error.name });
    return {
      statusCode: 500,
      body: JSON.stringify({ error: "Internal server error" }),
    };
  }
};
```

This is an illustrative pattern. Configure the table, IAM permissions, runtime, and API event mapping for the actual application.

## Recommended Architecture Workflow

1. Identify the event sources and user-facing entry points.
2. Define service boundaries and data ownership.
3. Choose synchronous or asynchronous processing based on the user experience and workload.
4. Design idempotency and failure handling before implementation.
5. Define infrastructure as code.
6. Apply least-privilege permissions.
7. Add logs, metrics, alarms, and tracing.
8. Test success, validation, retry, timeout, and failure paths.
9. Review deployment and rollback procedures.

## Production Readiness Checklist

- IAM policies follow least privilege.
- Secrets are stored outside source control.
- Functions validate incoming events.
- Retries and duplicate delivery are handled safely.
- Dead-letter or failure destinations are configured where needed.
- Timeouts and memory are appropriate for the workload.
- DynamoDB access patterns match the key design.
- Logs avoid sensitive information.
- Alarms cover meaningful operational failures.
- Infrastructure changes are reviewed before deployment.
- Recovery and rollback procedures are documented.

## Repository Contents

- `SKILL.md`: Main serverless architecture and implementation instructions.
- `README.md`: Service coverage, examples, and production checklist.

## Cost and Reliability

Serverless systems reduce infrastructure management but do not remove operational complexity. Consider invocation volume, execution duration, data transfer, storage, provisioned capacity, retries, and observability costs. Design for partial failures and duplicate events instead of assuming every invocation runs exactly once.
