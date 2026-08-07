-- Limitador de Velocidade com TextBox
-- Você escolhe a velocidade máxima

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- ===== CONFIGURAÇÃO =====
local VelocidadeMaxima = 120
local Ativado = false
-- =========================

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SpeedLimitGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Janela principal
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 260, 0, 150)
Frame.Position = UDim2.new(0.5, -130, 0.2, 0)
Frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 10)
UICorner.Parent = Frame

-- Botão X
local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -35, 0, 5)
CloseButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.Font = Enum.Font.GothamBold
CloseButton.TextSize = 16
CloseButton.Parent = Frame

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 6)
CloseCorner.Parent = CloseButton

-- Título
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -40, 0, 30)
Title.Position = UDim2.new(0, 10, 0, 5)
Title.BackgroundTransparency = 1
Title.Text = "Limitador de Velocidade"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Frame

-- TextBox
local TextBox = Instance.new("TextBox")
TextBox.Size = UDim2.new(0.85, 0, 0, 32)
TextBox.Position = UDim2.new(0.075, 0, 0.32, 0)
TextBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TextBox.TextColor3 = Color3.fromRGB(255, 255, 255)
TextBox.PlaceholderText = "Digite a velocidade máxima..."
TextBox.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
TextBox.Font = Enum.Font.Gotham
TextBox.TextSize = 15
TextBox.Text = tostring(VelocidadeMaxima)
TextBox.ClearTextOnFocus = false
TextBox.Parent = Frame

local TextBoxCorner = Instance.new("UICorner")
TextBoxCorner.CornerRadius = UDim.new(0, 6)
TextBoxCorner.Parent = TextBox

-- Botão Aplicar
local ApplyButton = Instance.new("TextButton")
ApplyButton.Size = UDim2.new(0.4, 0, 0, 30)
ApplyButton.Position = UDim2.new(0.075, 0, 0.58, 0)
ApplyButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
ApplyButton.Text = "Aplicar"
ApplyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ApplyButton.Font = Enum.Font.GothamBold
ApplyButton.TextSize = 14
ApplyButton.Parent = Frame

local ApplyCorner = Instance.new("UICorner")
ApplyCorner.CornerRadius = UDim.new(0, 6)
ApplyCorner.Parent = ApplyButton

-- Botão Ligar/Desligar
local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(0.4, 0, 0, 30)
ToggleButton.Position = UDim2.new(0.525, 0, 0.58, 0)
ToggleButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
ToggleButton.Text = "OFF"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Font = Enum.Font.GothamBold
ToggleButton.TextSize = 14
ToggleButton.Parent = Frame

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 6)
ToggleCorner.Parent = ToggleButton

-- Botão flutuante 🧂 pra abrir de novo
local OpenButton = Instance.new("TextButton")
OpenButton.Size = UDim2.new(0, 50, 0, 50)
OpenButton.Position = UDim2.new(0, 20, 0.5, -25)
OpenButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
OpenButton.Text = "🧂"
OpenButton.TextSize = 26
OpenButton.Font = Enum.Font.GothamBold
OpenButton.TextColor3 = Color3.fromRGB(255, 255, 255)
OpenButton.Active = true
OpenButton.Draggable = true
OpenButton.Visible = false
OpenButton.Parent = ScreenGui

local OpenCorner = Instance.new("UICorner")
OpenCorner.CornerRadius = UDim.new(1, 0)
OpenCorner.Parent = OpenButton

-- Funções do carro
local conexao = nil

local function GetVehicle()
    local char = LocalPlayer.Character
    if not char then return nil end
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    if not humanoid or not humanoid.SeatPart then return nil end
    return humanoid.SeatPart.Parent
end

local function GetPrimary(veiculo)
    return veiculo.PrimaryPart 
        or veiculo:FindFirstChild("Chassis") 
        or veiculo:FindFirstChild("Body") 
        or veiculo:FindFirstChildWhichIsA("BasePart")
end

local function LimitarVelocidade()
    local veiculo = GetVehicle()
    if not veiculo then return end
    local primary = GetPrimary(veiculo)
    if not primary then return end
    
    local velocity = primary.AssemblyLinearVelocity
    if velocity.Magnitude > VelocidadeMaxima then
        primary.AssemblyLinearVelocity = velocity.Unit * VelocidadeMaxima
        for _, part in ipairs(veiculo:GetDescendants()) do
            if part:IsA("BasePart") then
                local v = part.AssemblyLinearVelocity
                if v.Magnitude > VelocidadeMaxima then
                    part.AssemblyLinearVelocity = v.Unit * VelocidadeMaxima
                end
            end
        end
    end
end

local function Ligar()
    if conexao then return end
    Ativado = true
    ToggleButton.Text = "ON"
    ToggleButton.BackgroundColor3 = Color3.fromRGB(50, 180, 50)
    
    conexao = RunService.Heartbeat:Connect(function()
        if Ativado then
            LimitarVelocidade()
        end
    end)
end

local function Desligar()
    Ativado = false
    if conexao then
        conexao:Disconnect()
        conexao = nil
    end
    ToggleButton.Text = "OFF"
    ToggleButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
end

local function Toggle()
    if Ativado then
        Desligar()
    else
        Ligar()
    end
end

-- Botões
ApplyButton.MouseButton1Click:Connect(function()
    local valor = tonumber(TextBox.Text)
    if valor and valor > 0 then
        VelocidadeMaxima = valor
        print("Velocidade máxima definida para: " .. valor)
    else
        TextBox.Text = tostring(VelocidadeMaxima)
    end
end)

ToggleButton.MouseButton1Click:Connect(Toggle)

CloseButton.MouseButton1Click:Connect(function()
    Frame.Visible = false
    OpenButton.Visible = true
end)

OpenButton.MouseButton1Click:Connect(function()
    Frame.Visible = true
    OpenButton.Visible = false
end)

TextBox.FocusLost:Connect(function(enter)
    if enter then
        local valor = tonumber(TextBox.Text)
        if valor and valor > 0 then
            VelocidadeMaxima = valor
            print("Velocidade máxima definida para: " .. valor)
        end
    end
end)

-- Tecla L também liga/desliga
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.L then
        Toggle()
    end
end)

print("Limitador de Velocidade carregado!")
print("Digite a velocidade e clique em Aplicar")
print("Use o botão ON/OFF ou a tecla L")
