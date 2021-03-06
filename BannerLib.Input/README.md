# Input

The `HotKeyManager` type and associated views and functionality inside of Bannerlord is incredibly convoluted to work with, there are lots of gotchas and having conflicts between different mods registering hotkeys with the same ID's or mod names is almost a certainty, so this simple wrapper around it was created that handles the finer details and handles input polling so the entire thing is event based.

## Usage

```csharp
public class MySubModule : MBSubModuleBase
{
    private bool m_campaignStarted;
    protected override void OnSubModuleLoad()
    {
        // Create a new HotKeyManager for your mod.
        var hkm = HotKeyManager.Create("MyMod");
        // Add your HotKeyBase derived class to the manager.
        // You can add as many hotkeys as you'd like before building them up.
        // You can also use `hkm.Add(new TestKey(SomeExampleArgument))` if you'd like to have a non-default constructor.
        var rslt = hkm.Add<TestKey>();
        // It's not necessary to supply a predicate, it's just a convenience.
        // You can also manually set IsEnabled to more simply enable/disable a keys functionality.
        rslt.Predicate = () => m_campaignStarted;
        // Subscribe to each of the events on the hotkey at any time.
        rslt.OnReleasedEvent += () =>
            InformationManager.DisplayMessage(new InformationMessage("Test Key Released!", Colors.Magenta));
        // Call this to build up all the hotkeys you added.
        hkm.Build();
    }

    protected override void OnGameStart(Game game, IGameStarter gameStarterObject)
    {
        // An example just to demonstrate functionality.
        if (game.GameType is Campaign) m_campaignIsStarted = true;
    }
}

public class TestKey : HotKeyBase
{
    public TestKey() : base(nameof(TestKey))
    {
        DisplayName = "My Test Key";
        Description = "This is a test key.";
        DefaultKey = InputKey.Comma;
        Category = HotKeyManager.Categories[HotKeyCategory.CampaignMap];
    }
    
    protected override void OnReleased()
    {
        // You can also override methods relating to keypresses within the key itself.
    }
}
```

