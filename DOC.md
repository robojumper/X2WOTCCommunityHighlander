# X2WOTCCommunityHighlander Documentation

Welcome to the [X2WOTCCommunityHighlander](https://github.com/X2CommunityCore/X2WOTCCommunityHighlander/) documentation.

## Documentation Items

### ArmorEquipRollDLCPartChance ([#155](https://github.com/X2CommunityCore/X2WOTCCommunityHighlander/issues/155))

Tags: customization, compat

When a unit equips new armor, the game rolls from all customization options, even the ones where
the slider for the `DLCName` is set to `0`. The HL change fixes this, but if your custom armor only
has customization options with a `DLCName` set, the game may discard that `DLCName` (default: in 85% of cases)
which results in soldiers without torsos. If you want to keep having `DLCName`-only armor
(for example to display mod icons in `UICustomize`), you must disable that behavior
by creating the following lines in `XComGame.ini`:

```ini
[XComGame.CHHelpers]
+CosmeticDLCNamesUnaffectedByRoll=MyDLCName
```

#### Source code references

* [XComGameState_Unit.uc:7455-7467](https://github.com/X2CommunityCore/X2WOTCCommunityHighlander/blob/beta/X2WOTCCommunityHighlander/Src/XComGame/Classes/XComGameState_Unit.uc#L7455-L7467)
* [XComGame.ini:33-34](https://github.com/X2CommunityCore/X2WOTCCommunityHighlander/blob/beta/X2WOTCCommunityHighlander/Config/XComGame.ini#L33-L34)

### OverrideUnitFocusUI ([#257](https://github.com/X2CommunityCore/X2WOTCCommunityHighlander/issues/257))

Tags: tactical, compat

This focus change allows mods to change the focus UI that the vanilla game uses
to display Templar Focus. This effectively creates different types of Focus, even
though the game does not know about this. For example, you can create a custom
soldier class with its own type of focus, tracked with a `UnitValue`.
This imposes a few limitations on the system:

* A given unit only ever has a single "type" of focus. The rules for different focus
  types are expected to be so different from one another to make any conflicts
  a painful experience for modders and players.
  In particular, it means that this function should NOT be used to make any changes
  to the Templar Focus, as tempting as it may be.
* This also includes an Effect of the name `TemplarFocus` or an Effect Class of the type
  `XComGameState_Effect_TemplarFocus`.

In order to add your custom focus types, there are two changes in XComGame you can use:

* A new event hook for `UIUnitFlag` and `UITacticalHUD_SoldierInfo`: Documentation for that
  particular hook is directly below.
* A change in `X2AbilityCost_Focus`: You may subclass that particular class and override all
  functions declared there (`CanAfford`, `ApplyCost`, `PreviewFocusCost`). This can be used to
  preview a cost for custom skills that consume focus. Again, make sure to not mix and match
  custom subclasses with the base class for any abilities.

```unrealscript
EventID: OverrideUnitFocusUI
EventData: XComLWTuple {
    Data: [
      inout bool bVisible,
      inout int currentFocus,
      inout int maxFoxus,
      inout string color,
      inout string iconPath,
      inout string tooltipText,
      inout string focusLabel
    ]
}
```

Note that if `bVisible == false`, the rest will be ignored and will not have valid data in it.

Compatibility: If you override `UIUnitFlag`, your code may undo the HL's changes that
support this feature in the UI. See the tracking issue for code samples.

#### Source code references

* [CHHelpers.uc:327-370](https://github.com/X2CommunityCore/X2WOTCCommunityHighlander/blob/beta/X2WOTCCommunityHighlander/Src/XComGame/Classes/CHHelpers.uc#L327-L370)

