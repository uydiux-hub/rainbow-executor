local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "RainbowExecutor"
gui.ResetOnSpawn = false

local minimized = false

-- Main Frame
local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 400, 0, 300)
main.Position = UDim2.new(0.5, -200, 0.5, -150)
main.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true
Instance.new("UICorner", main).CornerRadius = UDim.new(0, 10)

-- Rainbow Gradient
local gradient = Instance.new("UIGradient", main)
gradient.Color = ColorSequence.new{
	ColorSequenceKeypoint.new(0.00, Color3.fromRGB(255, 0, 0)),
	ColorSequenceKeypoint.new(0.25, Color3.fromRGB(255, 255, 0)),
	ColorSequenceKeypoint.new(0.50, Color3.fromRGB(0, 255, 0)),
	ColorSequenceKeypoint.new(0.75, Color3.fromRGB(0, 255, 255)),
	ColorSequenceKeypoint.new(1.00, Color3.fromRGB(255, 0, 255))
}

-- Title
local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Text = "executor"
title.Font = Enum.Font.SourceSansBold
title.TextSize = 28
title.TextColor3 = Color3.fromRGB(255, 255, 255)

-- Script Box
local scriptBox = Instance.new("TextBox", main)
scriptBox.Size = UDim2.new(1, -20, 0, 180)
scriptBox.Position = UDim2.new(0, 10, 0, 40)
scriptBox.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
scriptBox.TextColor3 = Color3.fromRGB(255, 255, 255)
scriptBox.TextSize = 18
scriptBox.ClearTextOnFocus = false
scriptBox.TextWrapped = true
scriptBox.TextYAlignment = Enum.TextYAlignment.Top
scriptBox.TextXAlignment = Enum.TextXAlignment.Left
scriptBox.Font = Enum.Font.Code
scriptBox.Text = "print('Hello, World!')"
scriptBox.MultiLine = true
Instance.new("UICorner", scriptBox).CornerRadius = UDim.new(0, 8)

-- Button Holder
local buttonHolder = Instance.new("Frame", main)
buttonHolder.Size = UDim2.new(1, 0, 0, 50)
buttonHolder.Position = UDim2.new(0, 0, 1, -50)
buttonHolder.BackgroundTransparency = 1

local buttonTexts = {"Attach", "Clear", "Open", "Execute"}
local buttonColors = {
	Color3.fromRGB(255, 80, 80),
	Color3.fromRGB(255, 180, 50),
	Color3.fromRGB(50, 255, 120),
	Color3.fromRGB(80, 150, 255)
}

for i, text in pairs(buttonTexts) do
	local btn = Instance.new("TextButton", buttonHolder)
	btn.Size = UDim2.new(0.24, -5, 1, -10)
	btn.Position = UDim2.new((i - 1) * 0.25 + 0.01, 0, 0.05, 0)
	btn.Text = text
	btn.Font = Enum.Font.SourceSansBold
	btn.TextSize = 20
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.BackgroundColor3 = buttonColors[i]
	btn.BorderSizePixel = 0
	btn.Name = text
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)

	-- Functionalities
	if text == "Clear" then
		btn.MouseButton1Click:Connect(function()
			scriptBox.Text = ""
		end)

	elseif text == "Execute" then
		btn.MouseButton1Click:Connect(function()
			local success, err = pcall(function()
				loadstring(scriptBox.Text)()
			end)
			if not success then
				warn("Script Error: " .. err)
			end
		end)

	elseif text == "Open" then
		btn.MouseButton1Click:Connect(function()
			if getfiles and readfile and isfile then
				local files = getfiles(".")
				local selectedFile = nil
				for _, file in ipairs(files) do
					if file:match("%.txt$") or file:match("%.lua$") then
						selectedFile = file
						break
					end
				end
				if selectedFile and isfile(selectedFile) then
					local content = readfile(selectedFile)
					scriptBox.Text = content
					local success, err = pcall(function()
						loadstring(content)()
					end)
					if not success then
						warn("Script Error: " .. err)
					end
				else
					warn("ไม่พบไฟล์ .txt หรือ .lua ในโฟลเดอร์นี้")
				end
			elseif syn and syn.file and syn.file.select then
				local path = syn.file.select()
				if path and isfile(path) then
					local content = readfile(path)
					scriptBox.Text = content
					local success, err = pcall(function()
						loadstring(content)()
					end)
					if not success then
						warn("Script Error: " .. err)
					end
				end
			else
				warn("ฟังก์ชันเปิดไฟล์ไม่รองรับใน executor นี้")
			end
		end)

	elseif text == "Attach" then
		btn.MouseButton1Click:Connect(function()
			print("Attach clicked (placeholder)")
		end)
	end
end

-- Minimize Button
local minimize = Instance.new("TextButton", main)
minimize.Size = UDim2.new(0, 30, 0, 30)
minimize.Position = UDim2.new(1, -60, 0, 0)
minimize.Text = "-"
minimize.TextColor3 = Color3.fromRGB(255, 255, 255)
minimize.BackgroundTransparency = 1
minimize.MouseButton1Click:Connect(function()
	minimized = not minimized
	if minimized then
		scriptBox.Visible = false
		buttonHolder.Visible = false
		main.Size = UDim2.new(0, 400, 0, 30)
	else
		scriptBox.Visible = true
		buttonHolder.Visible = true
		main.Size = UDim2.new(0, 400, 0, 300)
	end
end)

-- Close Button
local close = Instance.new("TextButton", main)
close.Size = UDim2.new(0, 30, 0, 30)
close.Position = UDim2.new(1, -30, 0, 0)
close.Text = "X"
close.TextColor3 = Color3.fromRGB(255, 255, 255)
close.BackgroundTransparency = 1
close.MouseButton1Click:Connect(function()
	main.Visible = false
end)
