# How to use
## Backend
Add 'Inventory' MonoBehaviour to Game Object. This has the benefit of being able to inspect inventory slots in the editor in runtime.
To use, simply get a reference to the inventory object.

### Public Methods
- `AddItem()`
- `RemoveItem()`
- `GetItems()`
- `FillSpace()` 
- `ClearInventory()`
- `PrintInventory()`
- `IsEmpty()`
- `IsFull()`
### Events
- `OnItemAdded`
- `OnItemRemoved`
## UI 
![alt text](image.png "Built in UI Hierachy")

After that, call the `AssignInventory()` method to assign an inventory backend to display.
## Adding a Controller
The `InventoryController` Class:
```cs
using InventorySystem;
using System;
using UnityEngine;

[RequireComponent(typeof(Inventory))]
public class InventoryController : MonoBehaviour
{
    private Inventory inventory;
    [SerializeField] private UI_Inventory uiInventory;

    public event Action<ItemDataSO> OnItemCollected;
    private void Start()
    {
        inventory = GetComponent<Inventory>();
        uiInventory.AssignInventory(inventory);

    }
    public void AddCollectibleToInventory(ItemDataSO data)
    {
        inventory.AddItem(data, 1);
        OnItemCollected?.Invoke(data);
    }
}
```
