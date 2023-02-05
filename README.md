**QB-CORE** minigame for criminals when they get cuffed and are able to break out of them.
This works with PS-UI, If you need to change the mini game I will explain that at the bottom of the readme
If you want to change mini game I will not help further than what I posted below to help you change it.

**Thank you _https://github.com/varaxium_ for the code**

Dependencies
=
https://github.com/Project-Sloth/ps-ui

https://github.com/qbcore-framework/qb-core

https://github.com/qbcore-framework/qb-policejob

Find event in qb-policejob > Client > Interactions
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
        isHandcuffed = false -- May need to set this to true or false
        TriggerServerEvent("police:server:SetHandcuffStatus", false) -- May need to set this to true or false
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
        if not deadBozo and not lastStand then -- May need to change this
        local seconds = math.random(7,10)
        local circles = math.random(2,5)
        local success = exports['ps-ui']:Circle(function(success)
            if success then
                TriggerServerEvent("InteractSound_SV:PlayOnSource", "Uncuff", 0.2)
                QBCore.Functions.Notify("You slipped out", "success")
            else
                TriggerServerEvent("police:server:SetHandcuffStatus", true) -- May need to set this to true or false
                isHandcuffed = true -- May need to set this to true or false
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
Changes
=
You might have to change the line 

        if not deadBozo and not lastStand then
You will need to change the 
 ```
deadBozo
laststand
```
depending on what scripts you use for when a player dies

Minigame
=

If you want to change the speed and amount of circles you want change the ```1,``` and the ```5,``` on line 404
```
end, 1, 5) -- NumberOfCircles, MS
```

```1,``` being how many circles and ```5,``` being how fast the circle minigame spins around.

How change the minigame
=
```
exports['ps-ui']:Circle(function(success)
```
This is the export that ps-ui uses for the Circle mini game, to change this find the mini game you want to use and copy there snippet into this line

Example
= 
```
local time = 7
local circles = 1
exports['CircleMinigame']:StartLockPickCircle(circles, seconds, success)
```
What this does is grabs the local time and local cirlces for this mini game and uses it in the ```circles``` and ```seconds``` for the export, so the minigame would be 7 seconnds long and there would only be 1 circle you would have to do

What to delete / change
=
You will need to change and delete 2 things

**Delete this code below**
```
end, 1, 5) -- NumberOfCircles, MS
```
**You may need to change some of the code as well, Find 4 lines of code that I put ```--May need to change this to true or false``` If you are having issues of not being able to break out of the cuffs or the cuffing system is backwards to where you fail the mini game and get uncuffed but if you succsessfully do it and you get cuffed then mess around with those 4 lines of code**

The mini game used for the change is https://github.com/trclassic92/CircleMinigame

What your code should look like now
=
```
RegisterNetEvent('police:client:GetCuffed', function(playerId, isSoftcuff)
    local lastStand = QBCore.Functions.GetPlayerData().metadata["inlaststand"]
    local deadBozo = QBCore.Functions.GetPlayerData().metadata["isdead"]
    local ped = PlayerPedId()
    if not isHandcuffed then
        isHandcuffed = false -- May need to set this as true or false
        TriggerServerEvent("police:server:SetHandcuffStatus", false) -- May need to set this to true or false
        ClearPedTasksImmediately(ped)
        if GetSelectedPedWeapon(ped) ~= WEAPON_UNARMED then
            SetCurrentPedWeapon(ped, WEAPON_UNARMED, true)
        end
        if not isSoftcuff then
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
        local circles = math.random(2,4)
        local success = exports['CircleMinigame']:StartLockPickCircle(circles, seconds, success)     
        if success then
            TriggerServerEvent("InteractSound_SV:PlayOnSource", "Uncuff", 0.2)
            QBCore.Functions.Notify("You slipped out", "success")
        end

        if success then
            TriggerServerEvent("police:server:SetHandcuffStatus", true) -- May need to set this to true or false
            isHandcuffed = true -- May need to set this to true or false
            ClearPedTasksImmediately(ped)
        end
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

