#Recipe-and-Crafting-Rule-Engine
```mermaid

classDiagram
    class Item {
        - string itemId
        - string name
        - string type
        + getDetails() string
    }

    class Ingredient {
        - int quantity
        + getQuantity() int
    }

    class Recipe {
        - string recipeId
        - string name
        - bool isReversible
        - list<Ingredient> ingredients
        - list<CraftingConstraint> constraints
        + validate() bool
        + getIngredients() list<Ingredient>
    }

    class CraftingConstraint {
        - string toolRequired
        - int timeRequired
        - int sequenceOrder
        + checkConstraint() bool
    }

    class Inventory {
        - list<Item> items
        + addItem(Item item)
        + removeItem(string itemId)
        + hasItem(string itemId) bool
    }

    class CraftingEngine {
        - list<Recipe> recipes
        - Inventory inventory
        + craft(string recipeId) CraftingResult
        + validateRecipe(Recipe recipe) bool
        + resolveDependencies()
    }

    class CraftingResult {
        - bool success
        - string message
        - list<string> steps
        + getSummary() string
    }

    class Owner {
        - string username
        + createRecipe()
        + craftItem(Recipe recipe)
    }

    Ingredient --|> Item : inherits
    Recipe --> Ingredient : contains
    Recipe --> Item : produces
    Recipe --> CraftingConstraint : has many
    CraftingEngine --> Recipe : uses
    CraftingEngine --> Inventory : manages
    CraftingEngine --> CraftingResult : returns
    Owner --> Recipe : creates
    Owner --> CraftingEngine : uses
```




```mermaid
sequenceDiagram
    participant User
    participant CLI
    participant RuleEngine
    participant Inventory

    User->>CLI: craft iron_sword
    CLI->>RuleEngine: craft(Item("iron_sword"), inv)
    RuleEngine->>RuleEngine: canCraftRec(iron_sword, visited={})
    RuleEngine->>RuleEngine: Check recipe: needs 2x iron_ingot, 1x stick
    RuleEngine->>RuleEngine: canCraftRec(iron_ingot, visited={iron_sword})
    RuleEngine->>RuleEngine: Check recipe: needs iron_ore + furnace
    RuleEngine->>Inventory: has(furnace)? 
    Inventory-->>RuleEngine: true
    RuleEngine->>RuleEngine: canCraftRec(iron_ore, visited={iron_sword, iron_ingot})
    RuleEngine-->>RuleEngine: true, it's raw
    RuleEngine->>RuleEngine: craftRec(iron_ingot)
    RuleEngine->>Inventory: remove(iron_ore), add(iron_ingot)
    RuleEngine->>RuleEngine: craftRec(iron_sword)
    RuleEngine->>Inventory: remove(2x iron_ingot, 1x stick), add(iron_sword)
    RuleEngine-->>CLI: success
    CLI-->>User: Crafted iron_sword
```
