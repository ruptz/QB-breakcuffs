qb-core event minigame for criminals when they get cuffed and are able to break out of them.
This works with PS-UI, If you need to change the mini game I will explain that at the bottom of the readme

Thank you https://github.com/varaxium for the code

Find event in qb-policejob
=
```
RegisterNetEvent('police:client:GetCuffed', function(playerId, isSoftcuff)
```

Replace event with
=
```
RegisterNetEvent('police:client:GetCuffed', function(playerId, isSoftcuff)
    local lastStand = QBCore.Functions.GetPlayerData().metadata["inlaststand"]
    local deadBozo = QBCore.Functions.GetPlayerData().metadata["isdead"]
    local ped = PlayerPedId()
    if not isHandcuffed then
        isHandcuffed = false
        TriggerServerEvent("police:server:SetHandcuffStatus", false)
        ClearPedTasksImmediately(ped)
        if GetSelectedPedWeapon(ped) ~= WEAPON_UNARMED then
            SetCurrentPedWeapon(ped, WEAPON_UNARMED, true)
        end
        if isSoftcuff then
            cuffType = 16
            GetCuffedAnimation(playerId)
            QBCore.Functions.Notify(Lang:t("info.cuff"), 'primary')
        else
            cuffType = 49
            GetCuffedAnimation(playerId)
            QBCore.Functions.Notify(Lang:t("info.cuffed_walk"), 'primary')
        end
        if not deadBozo and not lastStand then
        local seconds = math.random(7,10)
        local circles = math.random(2,5)
        local success = exports['ps-ui']:Circle(function(success)
            if success then
                TriggerServerEvent("InteractSound_SV:PlayOnSource", "Uncuff", 0.2)
                QBCore.Functions.Notify("You slipped out", "success")
            else
                TriggerServerEvent("police:server:SetHandcuffStatus", true)
                isHandcuffed = true
                ClearPedTasksImmediately(ped)
            end
        end, 1, 5) -- NumberOfCircles, MS
    end
    else
        isHandcuffed = false
        isEscorted = false
        TriggerEvent('hospital:client:isEscorted', isEscorted)
        DetachEntity(ped, true, false)
        TriggerServerEvent("police:server:SetHandcuffStatus", false)
        ClearPedTasksImmediately(ped)
        TriggerServerEvent("InteractSound_SV:PlayOnSource", "Uncuff", 0.2)
        QBCore.Functions.Notify(Lang:t("success.uncuffed"),"success")
    end
end)
```

MINIGAME
=

If you want to change the speed and amount of circles you want change the ```1,``` and the ```5,``` on line 404
```
end, 1, 5) -- NumberOfCircles, MS
```

```1,``` being how many circles and ```5,``` being how fast the circle minigame spins around.

Change mini game
=
Find in the code I posted 
```
local success = exports['ps-ui']:Circle(function(success)
```
and
```
end, 1, 5) -- NumberOfCircles, MS
```
You will need to change your
```
exports['ps-ui']:Circle(function(success)
```
to the mini game export you want to use

example
= 
```
local time = 7
local circles = 1
local success = exports['qb-lock']:StartLockPickCircle(circles, time, success)
```
and delete the 

```
end, 1, 5) -- NumberOfCircles, MS
```
Since this is ps-ui

This code should now work with the minigame "qb-lock"
