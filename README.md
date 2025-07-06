-- MUZAN HUB SCRIPT COMPLETO

local NPCData = loadstring(game:HttpGet('https://raw.githubusercontent.com/SeuGitHub/MuzanHub/main/NPCData.lua'))()
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Remotes = ReplicatedStorage:WaitForChild("Remotes")

-- Interface
local Library = loadstring(game:HttpGet("https://github.com/ActualMasterOogway/Fluent-Renewed/releases/latest/download/Fluent.luau"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/ActualMasterOogway/Fluent-Renewed/master/Addons/SaveManager.luau"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/ActualMasterOogway/Fluent-Renewed/master/Addons/InterfaceManager.luau"))()

local Window = Library:CreateWindow{
    Title = "MUZAN HUB",
    SubTitle = "by MUZAN",
    Size = UDim2.fromOffset(830, 525),
    Theme = "Dark",
    Acrylic = true,
    MinimizeKey = Enum.KeyCode.RightControl
}

local Tabs = {
    Farm = Window:CreateTab{ Title = "Auto Farm", Icon = "swords" },
    TP = Window:CreateTab{ Title = "Teleport", Icon = "map" },
    Extras = Window:CreateTab{ Title = "Extras", Icon = "star" },
    Settings = Window:CreateTab{ Title = "Settings", Icon = "settings" }
}

-- Auto Farm
local SelectedWorld = nil
local SelectedNPC = nil

local WorldDropdown = Tabs.Farm:CreateDropdown("WorldSelect", {
    Title = "Selecione o Mapa",
    Values = {},
    Multi = false
})

for worldName, _ in pairs(NPCData) do
    table.insert(WorldDropdown.Values, worldName)
end

WorldDropdown:OnChanged(function(value)
    SelectedWorld = value
    NPCDropdown:SetValues({})
    for npcName, _ in pairs(NPCData[SelectedWorld]) do
        table.insert(NPCDropdown.Values, npcName)
    end
end)

local NPCDropdown = Tabs.Farm:CreateDropdown("NPCSelect", {
    Title = "Selecione o NPC",
    Values = {},
    Multi = false
})

NPCDropdown:OnChanged(function(value)
    SelectedNPC = value
end)

local AutoFarm = false
Tabs.Farm:CreateToggle("AutoFarmToggle", { Title = "Ativar Auto Farm", Default = false }):OnChanged(function(value)
    AutoFarm = value
    while AutoFarm do
        if SelectedWorld and SelectedNPC then
            for _, pos in ipairs(NPCData[SelectedWorld][SelectedNPC]) do
                LocalPlayer.Character:PivotTo(CFrame.new(pos))
                task.wait(0.2)
                Remotes.M1Request:FireServer(3)
                task.wait(0.5)
            end
        end
        task.wait(0.5)
    end
end)

-- Auto Rewards
Tabs.Extras:CreateButton{
    Title = "Coletar Rewards (1-12)",
    Callback = function()
        for i = 1, 12 do
            Remotes.RequestReward:InvokeServer(i)
            task.wait(0.2)
        end
    end
}

-- Rank Up
Tabs.Extras:CreateButton{
    Title = "Rank Up",
    Callback = function()
        Remotes.RankUp:InvokeServer()
    end
}

-- TP Ilhas
for worldName, data in pairs(NPCData) do
    Tabs.TP:CreateButton{
        Title = "TP para "..worldName,
        Callback = function()
            LocalPlayer.Character:PivotTo(CFrame.new(data.TP))
        end
    }
end

-- Join servidor
Tabs.Extras:CreateButton{
    Title = "Join servidor menor",
    Callback = function()
        -- join servidor menor (exemplo, precisa implementar com API ou script externo)
    end
}

Tabs.Extras:CreateButton{
    Title = "Join servidor normal",
    Callback = function()
        -- join servidor normal (exemplo, pode usar TeleportService)
    end
}

-- Configurações
SaveManager:SetLibrary(Library)
InterfaceManager:SetLibrary(Library)
SaveManager:SetFolder("MuzanHub")
InterfaceManager:SetFolder("MuzanHub")
InterfaceManager:BuildInterfaceSection(Tabs.Settings)
SaveManager:BuildConfigSection(Tabs.Settings)
SaveManager:LoadAutoloadConfig()

Library:Notify{ Title = "MUZAN HUB", Content = "Script carregado com sucesso!", Duration = 5 }
