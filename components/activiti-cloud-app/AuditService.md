# Audit Service

The Audit Service module provides Audit capabilities. This module is in charge of consume one or more Runtime Bundle Events and storing them into the
Event Store. Our default implementation consist of a simple JPA implementation. The Audit Service consume messages coming from a Stream
and store it as they arrive. The audit service doesn't do any data manipulation.

## Filters

## Implementations
 - JPA using Spring Data JPA
