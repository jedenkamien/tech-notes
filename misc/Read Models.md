## Read Models

Read Models are optimized for specific use cases such as database synchronization, messaging, or data presentation. Each object in a Read Model serves a specific purpose - for example, a ListedProductView object might be designed exclusively for product listing displays.

Unlike domain entities, Read Model objects contain only the information necessary for their particular use case. Their data can be aggregated from various sources such as Elasticsearch storage, session data, or multiple database tables.

A Read Model represents a view of your domain model from a specific perspective - the reading perspective. Since they are used exclusively for read operations, Read Models can be simple and highly optimized for performance.

### Key Characteristics of Read Models

- Purpose-built for specific read scenarios
- Contain only necessary data for their use case
- Can combine data from multiple sources
- Optimized for query performance
- Not intended for write operations
  
Read Models allow you to separate your read and write concerns, enabling you to optimize each independently according to their different requirements.
