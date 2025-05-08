# Adding Item Interaction
## Built In Class Method
Let's say we want a health potion item. When used, it will restore health for the player.
There is a built-in system for creating custom types for custom item functionality. Currently this supports item usage. In future, this may be extended to allow for item objects to receive update ticks, allow equiping etc.  

First we create our custom HealthPotion script:
```cs
public class HealthPotionItem : BaseInventoryItem, IUseable
{
    private Inventory inv;
    private Player player;
    public HealthPotionItem(GameObject owner) : base(owner)
    {
        inv = owner.GetComponent<Inventory>();
        player = owner.GetComponent<Player>();
    }

    public void Use(InventorySlot slot)
    {
        inv.RemoveFromSlot(slot, 1);
        player.RestoreHealth(50);
    }
}
```
Here are some things to take note of:  

- For this behaviour to be recognised, it must extend from `BaseInventoryItem`
- The `IUseable` interface adds the `Use()` method, which is called when user presses the 'Use' key while this item is selected
- The `owner` Game Object is passed as an argument in the constructor. This is called at runtime and provides the owner of the inventory holding the item
- It is good practice to get the references once in the inventory, and simply used the cached values in the `Use()` method

Next, go to the Item Scriptable Object, and select this `HealthPotionItem` type in the custom behaviour type dropdown.

And that's it! How simple is that :D

## Linking From External Script Using Events
Let's say we have a dagger item. We want to throw a dagger projectile and decrease stack size by 1 when used  

First we create a `WeaponThrower.cs` script.
```cs
[SerializeField] private Inventory inv;

private void OnEnable()
{
    inv.OnItemUsed += ThrowWeaponFromInv;
}

private void OnDisable()
{
    inv.OnItemUsed -= ThrowWeaponFromInv;
}
```
Here we do 2 things:

1. Get a reference to our inventory data model
2. Subscribe to the `OnItemUsed` event

Next, let's handle what actually happens when an item is used.  
We add this method:
```cs
private void ThrowWeaponFromInv(InventorySlot slot)
{
    if (slot.itemData.displayName == "Dagger") {
        ThrowWeapon(daggerPrefab, UtilScripts.GetVectorToMouse(throwingPoint.position));
        slot.RemoveFromStack(1);
    }
}
```
Take note that the `OnItemUsed` event passes the currently selected `InventorySlot` object as an argument.  
Let's see how the logic is handled:

1. First we check if the used item is the item we are looking for
2. We call the `ThrowWeapon` function, which I will not show here, but it will instantiate a prefab in the direction given
3. Lastly, it reduces stack count by 1

> If we want to consume 5 items, and only want the function to work if the stack size >5, we can do a simple check on the stack size before executing our logic, then call `RemoveFromStack(5)`.
