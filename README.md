-- Freio Instantâneo (Botão + Tecla R)
-- Para o carro na hora

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- ===== CONFIGURAÇÃO =====
local Ativado = false
-- ========================

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FreioGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Botão flutuante
local Button = Instance.new("TextButton")
Button.Size = UDim2.new(0, 140, 0, 50)
Button.Position = UDim2.new(0, 20, 0.5, 0)
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

-- Função que para o carro
local function PararCarro()
    local char = LocalPlayer.Character
    if not char then return end
    
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    if not humanoid or not humanoid.SeatPart then return end
    
    local seat = humanoid.SeatPart
    local veiculo = seat.Parent
    
    local primary = veiculo.PrimaryPart or veiculo:FindFirstChild("Chassis") or veiculo:FindFirstChild("Body") or seat
    
    if primary and primary:IsA("BasePart") then
        primary.AssemblyLinearVelocity = Vector3.zero
        primary.AssemblyAngularVelocity = Vector3.zero
    end
    
    for _, part in ipairs(veiculo:GetDescendants()) do
        if part:IsA("BasePart") then
            part.AssemblyLinearVelocity = Vector3.zero
            part.AssemblyAngularVelocity = Vector3.zero
        end
    end
end

-- Loop do freio
local conexao = nil

local function LigarFreio()
    if conexao then return end
    
    Ativado = true
    Button.Text = "FREIO: ON"
    Button.TextColor3 = Color3.fromRGB(80, 255, 80)
    
    conexao = RunService.Heartbeat:Connect(function()
        if Ativado then
            PararCarro()
        end
    end)
end

local function DesligarFreio()
    Ativado = false
    if conexao then
        conexao:Disconnect()
        conexao = nil
    end
    Button.Text = "FREIO: OFF"
    Button.TextColor3 = Color3.fromRGB(255, 80, 80)
end

local function ToggleFreio()
    if Ativado then
        DesligarFreio()
    else
        LigarFreio()
    end
end

-- Clique no botão
Button.MouseButton1Click:Connect(ToggleFreio)

-- Tecla R
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.R then
        ToggleFreio()
    end
end)

print("Freio Instantâneo carregado!")
print("Clique no botão OU aperte R para ligar/desligar")
