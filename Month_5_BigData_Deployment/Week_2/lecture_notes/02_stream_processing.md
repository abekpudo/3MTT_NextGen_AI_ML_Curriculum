Stream Processing

Overview
Introduce stream processing concepts, including event ingestion, windowing, stateful computation, and low-latency data delivery.

Why This Topic Matters
Streaming data pipelines are essential when decisions must be made in near real time, such as fraud detection, monitoring, and personalization.

Lecture Goals
- Distinguish streaming from batch processing
- Explain event time and processing time semantics
- Use windowing and aggregation for time-based computations
- Handle late-arriving and out-of-order data
- Plan for fault-tolerant state management

Key Concepts
- streaming
- windowing
- event time
- stateful processing
- latency

Classroom Example / Case Study
- Describe how a stream processor ingests continuous events and updates aggregates
- Explain the need for checkpoints and exactly-once or at-least-once semantics
- Show examples of popular streaming platforms

Practical Implementation Notes
- Keep state small and checkpointed.
- Design windows to match the business time semantics.
- Monitor lag and processing delays closely.

Tools & Libraries
- Kafka
- Spark Structured Streaming
- Flink
- AWS Kinesis
- Apache Pulsar

Recommended Resources
- Stream processing fundamentals
- Windowing and stateful stream processing

---
