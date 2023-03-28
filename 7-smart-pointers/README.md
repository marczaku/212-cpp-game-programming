# 7 Smart Pointers

## Problem:
In the following example: Do you get a
- pointer to a managed object, e.g. a field of the class?
- pointer to a dynamically allocated object?

```cpp
char* getString();
Player* createPlayer();
```

Why does the difference matter?

## Another Problem:
After calling these functions: Does the class you call it on
- still depend on the reference, i.e. do you need to make sure that it persists?
- not require the references anymore, i.e. you can/should delete it?
```cpp
void setName(char* name);
void initGame(Player* player);
```

## Problem Statement
In our application, we need full control over object lifetime.
- the best way of accomplishing this is dynamic memory allocation

But when allocating objects dynamically, we need to
- make sure that each object is deleted exactly once

How can we
- keep the amount of maintenance low?
- ensure that our functions clearly explain whether
  - it needs a resource temporarily
    - which means the caller should take care of cleaning up
  - it expects full ownership over a resource
    - which means the caller can ignore the resource from now on
  - it expects shared ownership over a resource
    - which means that the caller and the callee need to communicate and find out when both of them stopped requiring the resource
- avoid mistakes?

## Solution: Smart Pointers
Smart Pointers are Structs that hold pointers to dynamically allocated objects. And handle the de-allocation of said object following certain rules

The following examples will all use the following struct:

```cpp
struct Player {
    Player(const char* name) : name{ name } { printf("Player(%s);\n", name); }
    ~Player() { printf("~Player(%s);\n", name); }
    const char* name;
};
```

## Unique Pointer
- Transferable
- Exclusive

Used to hold references to objects which should only be owned by one class at a time. e.g.
- A Weapon (should only be held by one Unit at a time)

Note: It does not mean, that other classes cannot receive an address to the managed object. But the moment, the owner gets deleted, the owned object will get deleted.

### Construction
```cpp
std::unique_ptr<Player> spawnPlayer(const char* name) {
    return std::unique_ptr<Player>{new Player{name}};
}
```

```cpp
std::unique_ptr<Player> spawnPlayer(const char* name) {
    return std::make_unique<Player>(name);
}
```

### Use

#### Player Value Access

```cpp
handlePlayerValue(*localPlayer); // Player
```

#### Player Pointer Access
```cpp
handlePlayerPointer(localPlayer.get()); // Player*
```

#### Player Member Access
```cpp
localPlayer->name;
```

### Resource Management

#### Automatic De-Allocation on Scope Loss
```cpp
std::unique_ptr<Player> globalPlayer{};

int main() {
    {
        auto localPlayer = spawnPlayer("local");
        globalPlayer = spawnPlayer("global");
    } // ~Player(local)
    printf("End of Main.\n");
}
```

#### No Copies
You can't copy a Unique Pointer!
- this avoids that multiple classes think that
- they got a unique resource that they can clean up

```cpp
globalPlayer = localPlayer; // WON'T COMPILE!
```

#### Transfer Ownership
You can use `std::move` to transfer ownership
- if the moved to pointer already holds an object
  - it will destroy its current object
- then, it will take ownership of the moved-from object
```cpp
std::unique_ptr<Player> globalPlayer{};
int main() {
    {
        auto localPlayer = spawnPlayer("local");
        globalPlayer = spawnPlayer("global");
        globalPlayer = std::move(localPlayer); // ~Player(global)
    }
    printf("End of Main.\n");
}
```

### More
- `swap`
- `reset`
- `release`

## Shared Pointer
- Transferable
- Non-Exclusive

Used to hold references to objects that are supposed to be shareable between multiple classes. e.g.
- A Texture that is used by multiple Enemies
  - It should get freed from Memory as soon as
  - No Enemy needs that Texture anymore

### Construction
```cpp
std::shared_ptr<Player> spawnPlayer(const char* name) {
    return std::shared_ptr<Player>{new Player{name}};
}
```

```cpp
std::shared_ptr<Player> spawnPlayer(const char* name) {
    return std::make_shared<Player>(name);
}
```

### Use

#### Player Value Access

```cpp
handlePlayerValue(*localPlayer); // Player
```

#### Player Pointer Access
```cpp
handlePlayerPointer(localPlayer.get()); // Player*
```

#### Player Member Access
```cpp
localPlayer->name;
```

### Resource Management

#### Reference Counting
```cpp
std::shared_ptr<Player> globalPlayer{};
int main() {
    {
        auto localPlayer = spawnPlayer("local"); // use count 0 -> 1
        printf("Use Count: %ld\n", localPlayer.use_count()); // 1
        globalPlayer = localPlayer; // use count 1 -> 2
        printf("Use Count: %ld\n", localPlayer.use_count()); // 2
    } // localPlayer frees its use count 2 -> 1
    printf("Use Count: %ld\n", globalPlayer.use_count()); // 1
    printf("End of Main.\n");
}
```

#### Copies Are Legitimate
As the example above shows, this will simply mean that two variables reference the same object now:

```cpp
globalPlayer = localPlayer;
```

#### Transfer Ownership
You can use `std::move` to transfer ownership
- if the moved to pointer already holds an object
  - it will release its access to it
- then, it will retain the moved-from object
```cpp
std::shared_ptr<Player> globalPlayer{};
int main() {
    {
        auto localPlayer = spawnPlayer("local"); // use count 0 -> 1
        printf("Use Count: %ld\n", localPlayer.use_count()); // 1
        globalPlayer = std::move(localPlayer); // no change
        printf("Use Count: %ld\n", globalPlayer.use_count()); // 1
    }
    printf("Use Count: %ld\n", globalPlayer.use_count()); // 1
    printf("End of Main.\n");
}
```

### More
- `swap`
- `reset`
- `release`

## Weak Pointers
- Do not take Ownership

They make a great addition to previously mentioned pointers to give other classes safe access to owned objects without giving them ownership e.g.
- to share references to the Player with Enemies
- without enemies "owning" the Player

```cpp
struct Enemy {
    void setTarget(std::weak_ptr<Player> player) {
        this->player = player;
    }

    void attackTarget() {
        if (auto playerPtrShared = player.lock()) {
            printf("Player alive! Attacking Player.\n");
        }
        else {
            printf("Player is dead already!.\n");
        }
    }
private:
    std::weak_ptr<Player> player;
};

int main() {
    auto player = std::make_shared<Player>("player");
    Enemy enemy;
    enemy.setTarget(player);
    enemy.attackTarget(); // attack success
    player = {}; // free the Player object
    enemy.attackTarget(); // attack fail

}
```