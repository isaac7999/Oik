-- MiniCity Secure UI com funções adaptadas do script Rayfield

local CoreGui = game:GetService("CoreGui")
local Players = game:GetService("Players")
local player = Players.LocalPlayer

-- Evita múltiplas UIs
if CoreGui:FindFirstChild("MiniCitySecureUI") then
    CoreGui.MiniCitySecureUI:Destroy()
end

-- Proteção
local function proteger(obj)
    if syn and syn.protect_gui then
        syn.protect_gui(obj)
    elseif get_hidden_gui or gethui then
        local hiddenUI = get_hidden_gui and get_hidden_gui() or gethui()
        obj.Parent = hiddenUI
        return
    end
    obj.Parent = CoreGui
end

-- UI principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MiniCitySecureUI"
ScreenGui.ResetOnSpawn = false
proteger(ScreenGui)

-- Frame
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 320, 0, 240)
MainFrame.Position = UDim2.new(0.5, -160, 0.5, -120)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.BorderSizePixel = 0
MainFrame.BackgroundTransparency = 0.2
MainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
MainFrame.Parent = ScreenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = MainFrame

-- Título
local Title = Instance.new("TextLabel")
Title.Text = "Mini City - Painel Arthur"
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.Parent = MainFrame

-- Botão: Puxar Itens
local PuxarButton = Instance.new("TextButton")
PuxarButton.Size = UDim2.new(0.8, 0, 0, 40)
PuxarButton.Position = UDim2.new(0.1, 0, 0.4, 0)
PuxarButton.Text = "Puxar Itens"
PuxarButton.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
PuxarButton.TextColor3 = Color3.fromRGB(255, 255, 255)
PuxarButton.Font = Enum.Font.Gotham
PuxarButton.TextSize = 16
PuxarButton.Parent = MainFrame

local puxarCorner = Instance.new("UICorner")
puxarCorner.CornerRadius = UDim.new(0, 8)
puxarCorner.Parent = PuxarButton

PuxarButton.MouseButton1Click:Connect(function()
    local function deletarNotifyGui()
        local playerGui = game.Players.LocalPlayer:WaitForChild("PlayerGui")
        for _, gui in ipairs(playerGui:GetChildren()) do
            if gui.Name == "NotifyGui" and gui:IsA("ScreenGui") then
                gui:Destroy()
            end
        end
    end

    local itens = {"AK47", "Uzi", "Parafal", "Faca", "IA2", "G3", "IPhone 14", "Agua", "Hamburguer", "Hi Power", "Natalina"}
    local args = {
        [1] = "mudaInv",
        [2] = "1",
        [4] = "1"
    }

    task.spawn(function()
        while true do
            deletarNotifyGui()
            for i, item in ipairs(itens) do
                if i <= 16 then
                    args[2] = tostring(i)
                    args[3] = item
                    task.spawn(function()
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("InvRemotes"):WaitForChild("InvRequest"):InvokeServer(unpack(args))
                    end)
                end
            end
            wait(0.5) -- espera reduzida
        end
    end)
end)

-- Botão: Revistar (Mobile)
local RevistarButton = Instance.new("TextButton")
RevistarButton.Size = UDim2.new(0.8, 0, 0, 40)
RevistarButton.Position = UDim2.new(0.1, 0, 0.65, 0)
RevistarButton.Text = "Revistar (Mobile)"
RevistarButton.BackgroundColor3 = Color3.fromRGB(50, 150, 250)
RevistarButton.TextColor3 = Color3.fromRGB(255, 255, 255)
RevistarButton.Font = Enum.Font.Gotham
RevistarButton.TextSize = 16
RevistarButton.Parent = MainFrame

local revCorner = Instance.new("UICorner")
revCorner.CornerRadius = UDim.new(0, 8)
revCorner.Parent = RevistarButton

RevistarButton.MouseButton1Click:Connect(function()
    local TextChatService = game:GetService("TextChatService")

    local function sendRevistarMessage()
        local channel = TextChatService:WaitForChild("TextChannels"):WaitForChild("RBXGeneral")
        channel:SendAsync("/revistar morto")
    end

    sendRevistarMessage()
end)

-- Arrastar
local dragging, dragInput, dragStart, startPos

MainFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = MainFrame.Position

		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

MainFrame.InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = input.Position - dragStart
		MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
			startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)
