# ⚡ Arena Allocator in C++

A fast, lightweight, and thread-friendly **Header-Only Arena (Linear) Memory Allocator** written in C++11 and higher.

---

## 📌 What is an Arena Allocator?
An **Arena Allocator** (or Region-Based Allocator) allocates a single large contiguous block of memory upfront from the operating system. Subsequent memory allocations simply advance an internal pointer (`offset`), making allocation operations extremely fast **$O(1)$** with zero heap fragmentation.

Instead of calling `delete` on individual objects, the entire arena is reset at once using `Reset()`.

---

## 🚀 Features
- **Header-Only**: Easy integration into any project (just `#include "ArenaAllocator.h"`).
- **$O(1)$ Allocation Time**: Blazing fast compared to standard `malloc` or `new`.
- **Memory Alignment Support**: Guarantees byte alignment (supports `alignof`).
- **Zero Memory Fragmentation**: Eliminates heap fragmentation completely for temporary scopes.
- **Copy Protection**: Safe design preventing accidental shallow/deep copies (`= delete`).

---

## 🛠️ Usage Example

```cpp
#include <iostream>
#include "ArenaAllocator.h"

struct GameObject {
    int id;
    float health;

    GameObject(int id, float health) : id(id), health(health) {}
};

int main() {
    // 1. Create an Arena with 1 KB capacity
    ArenaAllocator arena(1024);

    // 2. Allocate space for an array of 5 integers
    int* numbers = new (arena.Allocate(sizeof(int) * 5)) int[5]{10, 20, 30, 40, 50};

    // 3. Allocate space for a custom struct using Placement New
    void* mem = arena.Allocate(sizeof(GameObject), alignof(GameObject));
    GameObject* player = new (mem) GameObject(1, 100.0f);

    std::cout << "Player Health: " << player->health << "\n";
    std::cout << "Memory Used: " << arena.GetUsedMemory() << " bytes\n";

    // 4. Instant wipe/reset of all allocations
    arena.Reset();
    
    return 0;
}
