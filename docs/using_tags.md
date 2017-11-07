# Using tags

For understanding and taking the most advantage of `ezplatform-http-cache`, understanding tags is key _(pun intended)_.
They work in a similar way as [persistence cache tags in eZ Platform v2](https://github.com/ezsystems/ezpublish-kernel/tree/7.0/doc/specifications/cache/persistence):
- A set of secondary keys set on every cache item, on top of "primary key" which in this case is the URI
- Like a index in a database it is typically used for anything relevant that represent the given cache item
- Used for cache invalidation

There is also other aspects to this bundle, like the fact by using Varnish xkey over BAN we can now do soft purge, but
more on that in [varnish/varnish.md](varnish/varnish.md).

For a bit less abstract way of saying all this; It allows us to do things like tagging every article response, and when
article content type gets an update we can tell Varnish all articles should be considered stale so they are updated in
the background once someone requests them.

## Tags in use in this bundle

- `content-<content-id>` :
  _Used on anything that are affected on changes to content, on content itself as well as location and so on._

- `content-type-<content-type-id>` :
  _For use when content type changes affecting content of it's type._

- `location-<location-id>` :
  _Used for clearing all cache relevant for a given location._
  
- `parent-<parent-location-id>` :
  _Useful for clearing all children of a parent, or in all siblings._

- `path-<location-id>` :
  _For operations that changes the tree itself, like move/remove/(..)._

- `relation-<content-id>` :
   _For use when updates affect their all reverse relations (NOTE: System does not add this tag to responses itself yet,
   just purges on it if present, response tagging with this is currently meant to be done inline in template logic / views
   where author knows if this should really happen or not)_
  

## How Response tagging is done

Response taggers

## How purge tagging is done

Slots
