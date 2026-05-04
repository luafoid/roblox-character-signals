# CharacterSignals

A lightweight Roblox module that keeps Humanoid signal connections alive across respawns and cleans up properly when players leave.

## The Problem

This is the most common silent bug in Roblox games:

```lua
player.CharacterAdded:Connect(function(character)
    local humanoid = character:WaitForChild("Humanoid")
    humanoid.Died:Connect(function()
        print("died")
    end)
end)
```

Works the first time. Stops firing after the second respawn. The old humanoid is gone and you never reconnected.

## Usage

```lua
local CharacterSignals = require(game.ServerScriptService.CharacterSignals)

game.Players.PlayerAdded:Connect(function(player)
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

`CharacterSignals.Watch(player, signals)` pass a table of Humanoid signal names to callbacks, starts watching immediately

`CharacterSignals.Unwatch(player)` stops watching and disconnects everything

## Install

Drop `CharacterSignals.lua` into `ServerScriptService` and require it from any server script.

## License

MIT
