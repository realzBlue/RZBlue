-- RedLBlue V2 - Versão Avançada
-- Auto Farm | ESP | Maestria | Anti Ban Completo

if not game:IsLoaded() then repeat wait() until game:IsLoaded() end
local Players, ReplicatedStorage, RunService = game:GetService("Players"), game:GetService("ReplicatedStorage"), game:GetService("RunService")
local LP = Players.LocalPlayer

-- Anti Ban Super Proteção
pcall(function()
    local mt = getrawmetatable(game)
    setreadonly(mt, false)
    local oldNamecall = mt.__namecall
    mt.__namecall = newcclosure(function(self, ...)
        local method = getnamecallmethod()
        if method == "Kick" or tostring(self):lower():find("kick") or tostring(self):lower():find("ban") then
            return wait(9e9)
        end
        return oldNamecall(self, ...)
    end)

    local oldIndex = mt.__index
    mt.__index = newcclosure(function(t, k)
        if k == "Kick" then
            return function() return wait(9e9) end
        end
        return oldIndex(t, k)
    end)

    -- Protege CoreGui
    local CoreGui = game:GetService("CoreGui")
    CoreGui.ChildAdded:Connect(function(c)
        if c.Name:lower():find("error") or c.Name:lower():find("ban") then
            c:Destroy()
        end
    end)

    -- Protege Reset
    local humanoid = LP.Character:WaitForChild("Humanoid")
    humanoid.BreakJointsOnDeath = false
    humanoid.Died:Connect(function()
        wait(0.1)
        humanoid.Health = 100
    end)

    -- RemoteBlock
    local BlockedEvents = {"Ban", "Kick", "Reset", "Error", "Report"}
    for _,desc in ipairs(getgc(true)) do
        if typeof(desc) == "function" and islclosure(desc) and getfenv(desc).script then
            for _,word in pairs(BlockedEvents) do
                if tostring(desc):lower():find(word:lower()) then
                    hookfunction(desc, function() return wait(9e9) end)
                end
            end
        end
    end
end)

-- ESP Avançado
local function CreateESP(obj, color)
    local text = Drawing.new("Text")
    text.Size = 13
    text.Center = true
    text.Outline = true
    text.Font = 2
    text.Color = color
    text.Visible = false

    RunService.RenderStepped:Connect(function()
        if obj and obj:FindFirstChild("HumanoidRootPart") and obj:FindFirstChild("Humanoid") and obj.Humanoid.Health > 0 then
            local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(obj.HumanoidRootPart.Position)
            if onScreen then
                text.Position = Vector2.new(pos.X, pos.Y)
                text.Text = obj.Name .. " ["..math.floor(obj.Humanoid.Health).."]"
                text.Visible = true
            else
                text.Visible = false
            end
        else
            text.Visible = false
        end
    end)
end

for _,v in pairs(workspace.Enemies:GetChildren()) do
    if v:FindFirstChild("HumanoidRootPart") then
        CreateESP(v, Color3.fromRGB(255,0,0))
    end
end

for _,v in pairs(workspace:GetChildren()) do
    if v:IsA("Model") and v.Name:find("Chest") then
        CreateESP(v, Color3.fromRGB(255,255,0))
    end
end

-- Auto Farm com Maestria
function EquipBest()
    local tools = LP.Backpack:GetChildren()
    for _,v in pairs(tools) do
        if v:IsA("Tool") and (v.Name:find("Combat") or v.Name:find("Sword") or v.Name:find("Gun") or v.Name:find("Fruit")) then
            v.Parent = LP.Character
            break
        end
    end
end

function GetClosestMob()
    local closest, distance = nil, math.huge
    for _,mob in pairs(workspace.Enemies:GetChildren()) do
        if mob:FindFirstChild("Humanoid") and mob:FindFirstChild("HumanoidRootPart") and mob.Humanoid.Health > 0 then
            local dist = (mob.HumanoidRootPart.Position - LP.Character.HumanoidRootPart.Position).Magnitude
            if dist < distance then
                distance = dist
                closest = mob
            end
        end
    end
    return closest
end

spawn(function()
    while task.wait() do
        pcall(function()
            EquipBest()
            local mob = GetClosestMob()
            if mob then
                repeat
                    LP.Character.HumanoidRootPart.CFrame = mob.HumanoidRootPart.CFrame * CFrame.new(0,10,0)
                    game:GetService("VirtualInputManager"):SendKeyEvent(true, "Z", false, game)
                    mob.Humanoid.Health -= 5
                    wait(0.15)
                until not mob or mob.Humanoid.Health <= 0
            end
        end)
    end
end)

-- Auto Haki
spawn(function()
    while wait(10) do
        pcall(function()
            ReplicatedStorage.Remotes.CommF_:InvokeServer("Buso")
        end)
    end
end)

OrionLib:MakeNotification({ Name = "RealZBlue", Content = "Loading Config Complete!!", Image = "rbxassetid://119980140458596", Time = 5 })
