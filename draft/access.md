 _Notes from ACL discussion on Google Wave:_

## Definitions

- MAC - Mandatory Access Control
- DAC - Discretionary Access Control
- RBAC - Role-Based Access Control
- AuthZ - Authorization ( what can you do?)
- AuthN - Authentication ( who are you?)

## Assumptions

Users will fall into the following groups, generally:

- Don't need authz other than "are you a users, k thx bye" (i.e. authn only)
- Only need authz for actions, ala MAC.
- Need row-level authz, ala DAC/*NIX.
- Need row-level and action-level authz, ala RBAC.
- Need complex logic-based authz, ala turing-complete functions.
- Need all of the above

## Notes

- A base implementation would probably have no other functionality than a "isAuthorized()" function that returns true/false. (Ed. note: method should probably be "check()", in keeping with other APIs)
- Progressively more complex adapters will enhance the granularity of the overall implementation.  All adapters will need to be able to act independently of each other and with each other.
- A failure to resolve authn does not necessarily imply an automatic failure to process custom rules (i.e., the 'use this token to create your account' scenario) (perhaps this indicates some level of processing weighting?)

## Use Cases

We'll follow the standard "Agile" definition for user stories for use cases, e.g. "as an X i would/need to be able to Y, so that i can Z".  X being a type of user of the system, Y being a general functionality and Z being the rationale for the necessity for the functionality.

- as an admin of any system, i want to limit access in an easy and transparent manner, so that i can prevent bad things from happening without having to do much work.

- as an admin of a simple system, i need to be able to authenticate myself and perhaps a few others to the system, so that i can indicate to the system that i am authorized to perform "registered user" functions.
- as an admin of a simple system, i need to be able to limit access to certain function based upon the "role" or "class" of a user, so that "normal" users cannot perform "admin" functions.
- as an admin of a simple system, i need to differentiate between myself and other admin users, so that i can control access to specific functionality like adding and removing users or granting permissions.

- as an admin of a moderately complex system, i need to be able to create groups of users, so that i can segregate groups of users into manageable "buckets".
- as an admin of a moderately complex system, i need to be able to limit access to functionality to specific groups or individual users, so that users cannot perform actions they are not authorized to perform.

- as an admin of a complex system, i need to be able to define a hierarchy of inherited permissions that span over multiple groups of users, so that i do not have to repeat myself everytime i create a group of people with similar access.
- as an admin of a complex system, i need to be able to override inherited permissions that a user receives, so that i do not have to create a new group for a user just because they are not allowed to access one thing that their group is able to access.
- as an admin of a complex system, i need to be able to have an unlimited number of groups or roles, because i do not know what the future holds.
- as an admin of a complex system, i need to be able to limit access to not just actions, but also data, so i can ensure that the proper group/roles can only act upon data that they have access to.

- as an admin of a very complex system, i need to limit access via custom logic, not just boolean rules, so that access is granted or denied on more complex scenarios than "does the requesting user's role and/or group's role have the requested action's permission".
- as an admin of a very complex system, i need to limit access to actions and data via both role-based rules *and* custom logic functions, so that i can create very fine-grained rules for who gets access to what as well as when or how they access it.
- as an admin of a very complex system, i need to attach weights/priorities to role-based rules vs. custom logic, so that i can determine which will cause access to succeed or fail first.

From these, let's expand into full-blown use cases, that are more process-based.  i wish we could use SAP gravity for the modelling!.  anywho.  please feel free to add more, remove redundant/retarded ones, or expound on any and all of them.

## Proposal

- A user has 1:N roles.
    - When no role is defined, a generic default role is assumed.
- A user belongs to 1:N groups.
    - When no group is defined, a generic default group is assumed.
- Role Permissions (RP) are then sum of all roles permissions.
- Group Permissions (GP) are then sum of all groups permissions.
- User Permissions are the intersection of RP and GP.

Do we just allow or also deny?

 **In the beginning we should have 'default deny' as the only option. If we find that the system needs additional flexibility (i.e. it is too hard to allow things in simple systems), then we can see about adding it.**

What about groups and roles? Do you think it would be too complex for this first approach?

While my opinion is that a user habtm groups, groups habtm roles, (with exceptions for user habtm roles) will pretty much cover the gamut of most usecases, i think most people will find it overblown.  *but*, using the case of a simple blog, a user could set up a couple roles (anonymous, admin, editor, with user A having extra permission to review and publish), and completely skip the group.  i think that would satisfy simple usage, and it's easy to visualize.

Re: allow/deny, i think the common consensus thus far is that we are going with default deny all, with additive permissions on top of that.  one thing we can take from cake acls is the usage of inherited tree permissions, in that by default we will create new rules as 'allow this for this aro/aco combination at this level and all children (1)', but we can also say inherit from parent (0) or 'deny this at this level and for all children' (-1).  i'll have to graph it out, but i'm sure either with INTs or bits we can do some sort of complimentary math on the MPTT to quickly get an answer to "can x do y?".

Nate, what's your thoughts on the tables required for this sort of approach?  a basic scenario would have...*counting*... 7 basic tables...

users
groups
roles
groups_users
groups_roles
roles_users
permissions (polymorphic)


Also, i'm sure that martin and i are in agreement, but what does everyone else think of using bits to combine permissions into a single row for each "object", ala *NIX?

This is useful, what we need to focus on right now is figuring out the API itself, and some common ways to configure ACL adapters, i.e.: "part of the adapter configuration is a closure that pulls an ID out of a user object".

Here's an API example that gives an overview of how adapters will be configured. Each adapter can have its own custom configurations, of course, but this is the basic idea of what we're working against.

```php
<?php

/**
 * In this example API spec, one or more adapters can be configured to manage different parts of
 * the framework, or possibly in tandem (only if we get to a point where that seems useful). Each
 * configuration has a name and an adapter, some settings that apply to all adapters (i.e. 'filter')
 * and some adapter-specific settings, i.e. closures that let you pass you pass in objects, which
 * return the specific data relevant to the adapter doing its lookup.
 */

use lithium\security\Access;

Access::config([
        'simple' => [
                'adapter' => 'SimpleTextBasedThing',
                // This adapter takes a user record and an HTTP request object and pulls out the information
                // to match against the rules defined in "access.txt".
                'data'    => LITHIUM_APP_PATH . '/config/access.txt',
                'filter'  => function($self, $params, $chain) {
                        // Any adapter can have a simple filter applied, which is defined the same as any
                        // other method filter in the framework. In this case, the filter checks to see if
                        // this is a weekday (no access on weekends!)
                        if (date('D') == 'Sat' || date('D') == 'Sun') {
                                return false;
                        }
                        return $chain->next($self, $params, $chain);
                }
        ],
        'complicated' => [
                'adapter' => 'DatabaseDrivenAdapterThing',
                /* This adapter can take two record objects and see if one can access the other */
        ],
        'other' => [
                'adapter' => 'TuringCompleteStateMachineThing',
                /* You get the idea */
        ]
]);

?>

// config/access.txt
-- deny everyone everything
deny    role    *                   *                               *

-- allow admins everything
allow   role    admin               *                               *

-- allow everyone to see error pages
allow   role    *                   *                               error

-- /posts
allow   role    *                   PostsController              index
allow   role    *                   PostsController              view
allow   role    editor_producer     PostsController              edit
allow   owner   *                   PostsController              edit
allow   role    editor_producer     PostsController              add
allow   role    grad_reporter       PostsController              add
allow   role    grad_moderator      PostsController              add
```

Usage would be along the lines of:

```php
$user = User::find($id);
Access::check("simple", $user, $this->request);

// - and / or -

$user = User::find($id);
$post = Post::find($this->request->id);
Access::check("complicated", $user, $post);
```

Ah, makes sense.  and what about row-level access control, for the more "complicated" adapters?

Once we have this base stuff done, wrapping model class interactions with code that runs these checks automatically (vis-a-vis CakePHP behaviors) should be trivial.

From here, we compile a series of real-world use cases that we want to address, and be as specific as possible. Literally in the form of:

1) User does X.

2) Y happens.

Once that's established, we figure out which adapters to implement first, which use cases they address, and how. Then we write them.

