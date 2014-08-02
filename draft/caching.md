
## Objectives

Caching in Lithium will be built-in right from the start, and will have these goals in mind:

 - Intuitive API
 - Provide a base for all caching used in the core
 - Easy to extend & add cache strategies
 - Availability of a cache engine should depend on the context; i.e. you **should not** be able to use the cache strategy designed for Views to cache Model queries.
 - **Context-based caching**: Perform sane key generation based on context. e.g. full page caching should use different parameters for key generation than query caching, but do this **transparently**. The user should never need to know that the key that he selects for an element cache (example) is supplemented with additional parameters to ensure the uniqueness of his cache key based on the context which it is in.

## High Level Architecture

- Adapter-driven base.
    - Any cache adapter which writes directly to some sort of persistent store (as opposed to a cache strategy which wraps a storage adapter with additional functionality).
    - General Cache API:
        - Cache::write( (string) $name, (string|int) $key, (string|array) $value, $conditions = array())
        - Cache::read( (string) $name, (string|int) $key, $conditions = array())
        - Cache::delete( (string) $name, (string|int) $key, $conditions = array())
        - Cache::fetch( (string) $name, (string|int) $key,  $closure, $conditions = array())
        - Cache::adapter( (string) $name, $options)
        - Cache::clean( (string) $name = null)
        - Cache::clear( (string) $name = null)

### Cache::write

The cache strategy being used is identified by $name (consistent across all methods. Write takes a key (either string or integer), and writes $value to the storage engine using the given key. Note that the key itself will be normalized to something that encapsulates the context in which the Cache::write option was made, and the user need not know this is even happening.

### Cache::read

Reads from cache store based on $key given. Again, keys are normalized to be contextually-dependent.

### Cache::delete

Deletes from cache store based on $key.

### Cache::fetch

A convenience method. You call Cache::fetch with the specified key. If the key does not exist in the cache, then $closure is run & the returned value is stored in the cache with the given key. Obviously, Cache::fetch does not introduce any new functionality, but allows for more concise & intelligent cache implementations.

### Cache::adapter

Configures the cache adapter (and perhaps strategy?) at run-time.

### Cache::clean

Garbage collection

### Cache::clear

Deletes all keys from cache storage

## General

Cache adapters will be configured in a similar manner to Connections, allowing us to retain some consistency across adapter configuration methods.

## Strategy-Based Caching

- Make it extremely simple for a user to define a custom cache strategy that can easily use other strategies & adapters.
- Strategies can optionally use several different adapters. Could be useful for fall-back situations.
- A strategy can wrap another strategy.

In addition to allowing the user a greater level of flexibility in defining & re-using cache adapters and strategies, this will allow us to programmatically build up the cache strategies to be used internally.

Example: a File cache adapter is wrapped by an Element cache strategy, which provides the specifics that are needed for that use case. For full page caching: File cache adapter + Page cache strategy, and so on. Since strategies can be applied to adapters as well as other strategies, the reusability of these is greatly increased.

**Example**: (add one here)

 - Serialization strategy
 - Compression strategy (gzip)
 - Cipher strategy (e.g. blowfish encrypt/decrypt)
 - Write-through strategy

