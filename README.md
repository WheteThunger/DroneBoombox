## Features

- Allows players with permission to deploy boomboxes onto RC drones
- Redirects damage from the boombox to the drone

## Known issues

Since the March 2023 Rust update, drones now sway in the wind, but attached entities do not sway. This causes undesirable visuals to players observing a drone that is being controlled. There is no known fix at this time.

## Permissions

- `droneboombox.deploy` -- Allows the player to deploy a boombox onto a drone using the `droneboombox` command.
- `droneboombox.deploy.free` -- Allows using the `droneboombox` command for free (no boombox item required).
- `droneboombox.autodeploy` -- Drones deployed by players with this permission will automatically have a boombox, free of charge.
  - Not recommended if you want to allow players to deploy other attachments such as auto turrets since they are incompatible.

## Commands

- `droneboombox` -- Deploys a boombox onto the drone the player is looking at, consuming a boombox item from their inventory unless they have permission for free boomboxes.

## Configuration

Default configuration:

```json
{
  "TipChance": 25
}
```

- `TipChance` (`0` - `100`) -- Chance that a tip message will be shown to a player when they deploy a drone, informing them that they can use the `/droneboombox` command. Only applies to players with the `droneboombox.deploy` permission who do not have the `droneboombox.autodeploy` permission.

## Localization

```json
{
  "Tip.DeployCommand": "Tip: Look at the drone and run <color=yellow>/droneboombox</color> to deploy a boombox.",
  "Error.NoPermission": "You don't have permission to do that.",
  "Error.NoDroneFound": "Error: No drone found.",
  "Error.BuildingBlocked": "Error: Cannot do that while building blocked.",
  "Error.NoBoomboxItem": "Error: You need a boombox to do that.",
  "Error.AlreadyHasBoombox": "Error: That drone already has a boombox.",
  "Error.IncompatibleAttachment": "Error: That drone has an incompatible attachment.",
  "Error.DeployFailed": "Error: Failed to deploy boombox.",
  "Error.CannotPickupWithCassette": "Cannot pick up that drone while its boombox contains a cassette."
}
```

## Recommended compatible plugins

Drone balance:
- [Drone Settings](https://umod.org/plugins/drone-settings) -- Allows changing speed, toughness and other properties of RC drones.
- [Targetable Drones](https://umod.org/plugins/targetable-drones) -- Allows RC drones to be targeted by Auto Turrets and SAM Sites.
- [Limited Drone Range](https://umod.org/plugins/limited-drone-range) -- Limits how far RC drones can be controlled from computer stations.

Drone fixes and improvements:
- [Better Drone Collision](https://umod.org/plugins/better-drone-collision) -- Overhauls RC drone collision damage so it's more intuitive.
- [Auto Flip Drones](https://umod.org/plugins/auto-flip-drones) -- Auto flips upside-down RC drones when a player takes control.
- [Drone Hover](https://umod.org/plugins/drone-hover) -- Allows RC drones to hover in place while not being controlled.

Drone attachments:
- [Drone Lights](https://umod.org/plugins/drone-lights) -- Adds controllable search lights to RC drones.
- [Drone Turrets](https://umod.org/plugins/drone-turrets) -- Allows players to deploy auto turrets to RC drones.
- [Drone Storage](https://umod.org/plugins/drone-storage) -- Allows players to deploy a small stash to RC drones.
- [Drone Boombox](https://umod.org/plugins/drone-boombox) (This plugin) -- Allows players to deploy boomboxes to RC drones.
- [Ridable Drones](https://umod.org/plugins/ridable-drones) -- Allows players to ride RC drones by standing on them or mounting a chair.

## Screenshots

![Boombox on a drone](https://github.com/WheteThunger/DroneBoombox/blob/master/DroneBoombox.png?raw=true)

## Developer API

```csharp
DeployableBoomBox API_DeployBoombox(Drone drone, BasePlayer player)
```

- Deploys a boombox onto the specified drone
- Returns the boombox entity if successfully deployed
- Returns `null` if the boombox was not deployed, due to it already having an incompatible attachment, or due to another plugin blocking it

## Developer Hooks

#### OnDroneBoomboxDeploy

```csharp
bool? OnDroneBoomboxDeploy(Drone drone, BasePlayer optionalDeployer)
```

- Called when a boombox is about to be deployed onto a drone
- Returning `false` will prevent the boombox from being deployed
- Returning `null` will result in the default behavior

Note: The `BasePlayer` argument will be `null` if the container is being deployed automatically (not via the `droneboombox` command).

#### OnDroneBoomboxDeployed

```csharp
void OnDroneBoomboxDeployed(Drone drone, StorageContainer stash, BasePlayer optionalDeployer)
```

- Called after a boombox has been deployed onto a drone
- No return behavior

Note: The `BasePlayer` argument will be `null` if the container was deployed automatically (not via the `droneboombox` command).

#### OnEntityBuilt

This is an Oxide hook that is normally called when deploying a boombox or other deployable. To allow for free compatibility with other plugins, this plugin calls this hook whenever a boombox is deployed onto a drone for a player.

- Not called when a boombox is deployed via the API without specifying a player
- The `Planner` can be used to get the player or the boombox item, while the `GameObject` can be used to get the deployed boombox

```csharp
void OnEntityBuilt(Planner planner, GameObject go)
```
