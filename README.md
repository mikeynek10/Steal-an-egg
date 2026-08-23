-- 1. Script Steal An Eggs
loadstring(game:HttpGet("https://raw.githubusercontent.com/WhiteX1208/Scripts/refs/heads/main/StealAnEggs.luau"))()

-- 2. Tắt hiệu ứng & Giảm lag
local Lighting = game:GetService("Lighting")
Lighting.GlobalShadows = false
Lighting.FogEnd = 9e9
Lighting.Brightness = 1
pcall(function() sethiddenproperty(Lighting, "Technology", Enum.Technology.Compatibility) end)

for _, v in pairs(Lighting:GetChildren()) do
    if v:IsA("PostEffect") or v:IsA("Atmosphere") or v:IsA("Bloom") or v:IsA("Blur") then
        v:Destroy()
    end
end

-- 3. Xử lý Vật thể + Instant E (Dành cho các nút hiện tại)
for _, v in pairs(game:GetDescendants()) do
    if v:IsA("ProximityPrompt") then
        v.HoldDuration = 0
    elseif v:IsA("ParticleEmitter") or v:IsA("Smoke") or v:IsA("Fire") or v:IsA("Sparkles") or v:IsA("Trail") then
        v.Enabled = false
    elseif v:IsA("BasePart") and not v:IsA("MeshPart") then
        v.Material = Enum.Material.SmoothPlastic
        v.Reflectance = 0
    end
end

-- 4. Xử lý các vật thể mới xuất hiện sau đó
game:GetService("Workspace").DescendantAdded:Connect(function(v)
    if v:IsA("ProximityPrompt") then
        v.HoldDuration = 0
    elseif v:IsA("ParticleEmitter") or v:IsA("Smoke") or v:IsA("Fire") or v:IsA("Sparkles") or v:IsA("Trail") then
        v.Enabled = false
    end
end)
