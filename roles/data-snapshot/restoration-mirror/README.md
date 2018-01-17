This role acts as a web-accessible backup of the base Data Snapshot.

It works by hosting as an up-to-date mirror of the origin repository.

Should the origin fail, restoration can occur from this location.

TODO: Assess the complications that could occur should the origin fail before this has had a chance to update, but after something else has updated.

## Design Exploration

### Background

The Origin Server contains a number of commits in a git repository. Let them be commits `A`, `B`, and `C`.

Other services (Restoration Mirrors, the IATI Dashboard, etc) obtain a copy of the data by cloning the git repository. When these services clone the repository with the initial state, every service will have a copy of commits `A`, `B`, and `C`.

Let services that are not the Origin Server or a Restoration Mirror be External Services.

Let the Origin Server create a 4th commit, `D`.

### `pull` method

Under this method, only Restoration Mirrors should clone from an Origin Server. All other services should clone from a mirror of a Data Snapshot Origin. This is to prevent race conditions that could occur on service restoration.

All services obtain a copy of the Origin by using a `pull` request that they instantiate. The Origin Server does not need to know of any other services. All services need to know of the Origin Server.

#### Race Condition Explanation

After creating `D`, a External Service `pull`s the commit and the Origin Server fails before the Restoration Mirror has cloned the commit.

A new Origin Server is then created from the Restoration Mirror. This new Origin Server lacks commit `D`.

The new Origin Server creates commit `E` for the next set of changes. This is based on commit `C`.

When the External Service next `pull`s, it will expect a commit based on `C`. This will not be the case, so it will be confused.

#### Race Condition Solution

If the External Service only clones from the Restoration Mirror, it is guaranteed that it will never have a commit that the Restoration Mirror does not. As such, the problem is removed.

### `push` method

In this method, the Origin Server updates all Replication Mirrors using a `push` command. External Services still follow the `pull` method.

The Origin Server needs to know the location of all Restoration Mirrors.

#### Explanation

The basic implementation of this has all of the same problems as the `pull` method, but the problem timeframe is smaller.

#### Solution

The Origin Server maintains two branches, one that has the latest information for use by Backups, and one that should be used by External Services. The second branch is only updated to contain data that the Origin Server knows exists on a Restoration Mirror.

This solution is somewhat dangerous since a basic setup leaves External Services are able to access data that is only on the branch for the Restoration Mirrors.

### Combined method

A best-of-both-worlds approach could also be used:

* External Services only clone from a Restoration Mirror
* Restoration Mirrors are updated by the Origin Server via a `push` method

In this situation, External Services cannot gain access to commits that are unavailable on a Restoration Mirror. Restoration Mirrors are also updated as soon as changes occur rather than having to wait until the next poll of the Origin Server.
