local CORRECT_KEY = "VN"
local CURRENT_VERSION = 1.0

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local targetGuiContainer = player:WaitForChild("PlayerGui")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "VN_LoaderSystem"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = targetGuiContainer

local KeyFrame = Instance.new("Frame")
KeyFrame.Size = UDim2.new(0, 300, 0, 150)
KeyFrame.Position = UDim2.new(0.5, -150, 0.4, -75)
KeyFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
KeyFrame.BorderSizePixel = 2
KeyFrame.BorderColor3 = Color3.fromRGB(0, 255, 255)
KeyFrame.Visible = true
KeyFrame.Parent = ScreenGui

local KeyTitle = Instance.new("TextLabel")
KeyTitle.Size = UDim2.new(1, 0, 0, 30)
KeyTitle.Text = "VN LOADER v" .. tostring(CURRENT_VERSION)
KeyTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyTitle.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
KeyTitle.Font = Enum.Font.SourceSansBold
KeyTitle.TextSize = 14
KeyTitle.Parent = KeyFrame

local KeyInput = Instance.new("TextBox")
KeyInput.Size = UDim2.new(0.8, 0, 0, 35)
KeyInput.Position = UDim2.new(0.1, 0, 0.35, 0)
KeyInput.PlaceholderText = "Nhập Key tại đây..."
KeyInput.Text = ""
KeyInput.TextColor3 = Color3.fromRGB(0, 0, 0)
KeyInput.BackgroundColor3 = Color3.fromRGB(240, 240, 240)
KeyInput.Font = Enum.Font.SourceSans
KeyInput.TextSize = 16
KeyInput.Parent = KeyFrame

local SubmitButton = Instance.new("TextButton")
SubmitButton.Size = UDim2.new(0.5, 0, 0, 30)
SubmitButton.Position = UDim2.new(0.25, 0, 0.70, 0)
SubmitButton.Text = "TẢI SCRIPT"
SubmitButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SubmitButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
SubmitButton.Font = Enum.Font.SourceSansBold
SubmitButton.TextSize = 14
SubmitButton.Parent = KeyFrame

local function activateAntiBan()
    local rawmetatable = getrawmetatable or debug.getmetatable
    local make_writeable = setreadonly or make_writeable
    if rawmetatable and make_writeable then
        local mt = rawmetatable(game)
        make_writeable(mt, false)
        local old_index = mt.__index
        mt.__index = newcclosure(function(self, index)
            if tostring(self) == "Humanoid" and index == "WalkSpeed" then
                return 16
            end
            if tostring(self) == "Humanoid" and index == "JumpPower" then
                return 50
            end
            return old_index(self, index)
        end)
    end
end

local function activateInvisible()
    local char = player.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    
    local clone = root:Clone()
    clone.Parent = char
    root:Destroy()
    char.PrimaryPart = clone
    
    for _, part in pairs(char:GetDescendants()) do
        if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
            part.Transparency = 1
            part.CanCollide = false
        elseif part:IsA("Decal") then
            part:Destroy()
        end
    end
end

local function checkUpdateAndExecute()
    activateAntiBan()
    activateInvisible()

    player.CharacterAdded:Connect(function()
        task.wait(0.5)
        activateInvisible()
    end)

    print("[VN System] Hệ thống đã được kích hoạt thành công!")
end

SubmitButton.MouseButton1Click:Connect(function()
    if KeyInput.Text == CORRECT_KEY then
        ScreenGui:Destroy() 
        checkUpdateAndExecute()
    else
        KeyInput.Text = ""
        KeyInput.PlaceholderText = "SAI KEY! Không thể loadstring."
    end
end)
