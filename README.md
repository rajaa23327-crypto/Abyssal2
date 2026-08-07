-- Freio Total v2
-- Trava o carro completamente

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- ===== CONFIGURAÇÃO =====
local Ativado = false
-- =========================

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FreioTotalGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Button = Instance.new("TextButton")
Button.Size = UDim2.new(0, 150, 0, 50)
Button.Position = UDim2.new(0, 20, 0.55, 0)
Button.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Button.Text = "FREIO: OFF"
Button.TextColor3 = Color3.fromRGB(255, 80, 80)
Button.Font = Enum.Font.GothamBold
Button.TextSize = 14
Button.Active = true
Button.Draggable = true
Button.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 10)
UICorner.Parent = Button

local conexao = nil
local pecasAncoradas = {}

local function GetVehicle()
    local char = LocalPlayer.Character
    if not char then return nil end
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    if not humanoid or not humanoid.SeatPart then return nil end
    return humanoid.SeatPart.Parent
end

local function TravarCarro()
    local veiculo = GetVehicle()
    if not veiculo then return end
    
    for _, part in ipairs(veiculo:GetDescendants()) do
        if part:IsA("BasePart") then
            part.AssemblyLinearVelocity = Vector3.zero
            part.AssemblyAngularVelocity = Vector3.zero
            
            if not part.Anchored then
                part.Anchored = true
                table.insert(pecasAncoradas, part)
            end
        end
    end
end

local function DestravarCarro()
    for _, part in ipairs(pecasAncoradas) do
        if part and part.Parent then
            part.Anchored = false
        end
    end
    table.clear(pecasAncoradas)
end

local function Ligar()
    if conexao then return end
    Ativado = true
    Button.Text = "FREIO: ON"
    Button.TextColor3 = Color3.fromRGB(80, 255, 80)
    
    conexao = RunService.Heartbeat:Connect(function()
        if Ativado then
            TravarCarro()
        end
    end)
end

local function Desligar()
    Ativado = false
    if conexao then
        conexao:Disconnect()
        conexao = nil
    end
    DestravarCarro()
    Button.Text = "FREIO: OFF"
    Button.TextColor3 = Color3.fromRGB(255, 80, 80)
end

local function Toggle()
    if Ativado then
        Desligar()
    else
        Ligar()
    end
end

Button.MouseButton1Click:Connect(Toggle)

-- Tecla L para ligar/desligar
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.L then
        Toggle()
    end
end)

print("Freio Total carregado!")
print("Clique no botão ou aperte L para ligar/desligar")
