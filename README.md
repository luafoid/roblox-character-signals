# CharacterSignals

A small Roblox module that keeps Humanoid signal connections alive across respawns and cleans up automatically when players leave.

## The Problem

This is one of the most common silent bugs in Roblox games:

```lua
player.CharacterAdded:Connect(function(character)
    local humanoid = character:WaitForChild("Humanoid")
    humanoid.Died:Connect(function()
        print("died")
    end)
end)
```

Works the first time. Stops firing after the next respawn. The old Humanoid is gone and the connection was never rebuilt for the new one.

CharacterSignals handles the reconnect for you, validates inputs, and cleans up on leave.

## Usage

```lua
local Players = game:GetService("Players")
local CharacterSignals = require(game.ServerScriptService.CharacterSignals)

Players.PlayerAdded:Connect(function(player)
    CharacterSignals.Watch(player, {
        Died = function(player, character)
            print(player.Name .. " died")
        end,
        Running = function(player, character, speed)
        end,
    })
end)
```

Reconnects every respawn. Cleans up on leave. No leaks.

## API

`CharacterSignals.Watch(player, signals)`
Watches a player. `signals` is a table mapping Humanoid signal names to callbacks. Callbacks receive `(player, character, ...)` where `...` is whatever the signal passes. Calling Watch again on the same player replaces the previous binding.

`CharacterSignals.Unwatch(player)`
Stops watching and disconnects everything for that player.

## Behavior

- Reconnects automatically on every respawn
- Drops connections immediately on `CharacterRemoving` so stale callbacks cant fire
- Ignores events from old characters if a respawn happens mid signal
- Warns on invalid signal names instead of failing silently
- Warns if the Humanoid never appears within 10 seconds
- Cleans up on `PlayerRemoving` automatically

## Install

Drop `CharacterSignals.lua` into `ServerScriptService` and require it from any server script.

## License

MIT
