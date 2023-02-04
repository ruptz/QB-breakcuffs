qb-core event minigame for criminals when they get cuffed and able to break out of them.

Find event in qb policejob
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


        end, 1, 5) -- NumberOfCircles, MS


```1,``` being how many circles and ```5,``` being how fast the circle minigame spins around.
