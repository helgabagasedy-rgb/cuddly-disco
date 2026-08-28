--==================================================
-- SLEEPY HUB V5
-- AUTO FARM + AUTO INTERACT + ANTI AFK
--==================================================

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer

--==================================================
-- CONFIG
--==================================================

local points = {
	Vector3.new(970, 36, -61),
	Vector3.new(1000, 36, -77),
	Vector3.new(974, 36, -92),
	Vector3.new(983, 36, -77)
}

local fallbackPosition =
	Vector3.new(769, 628, -1041)

local CHECK_INTERVAL = 300 -- 5 menit

local autoFarm = false
local autoInteract = false
local antiAFK = false

local currentTween = nil

--==================================================
-- CHARACTER
--==================================================

local function getCharacter()

	local char =
		player.Character or player.CharacterAdded:Wait()

	local hrp =
		char:WaitForChild("HumanoidRootPart")

	return char, hrp
end

--==================================================
-- GUI
--==================================================

local gui = Instance.new("ScreenGui")
gui.Name = "SleepyHubV5"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local main = Instance.new("Frame")

main.Size =
	UDim2.new(0,350,0,455)

main.Position =
	UDim2.new(0.5,-175,0.5,-227)

main.BackgroundColor3 =
	Color3.fromRGB(18,18,25)

main.BorderSizePixel = 0
main.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius =
	UDim.new(0,18)
corner.Parent = main

local stroke = Instance.new("UIStroke")
stroke.Color =
	Color3.fromRGB(75,75,100)
stroke.Thickness = 1.5
stroke.Transparency = 0.2
stroke.Parent = main

--==================================================
-- TITLE
--==================================================

local title = Instance.new("TextLabel")

title.Size =
	UDim2.new(1,-70,0,40)

title.Position =
	UDim2.new(0,20,0,8)

title.BackgroundTransparency = 1

title.Text =
	"SLEEPY  •  HUB V5"

title.TextColor3 =
	Color3.fromRGB(255,255,255)

title.TextSize = 20

title.Font =
	Enum.Font.GothamBold

title.TextXAlignment =
	Enum.TextXAlignment.Left

title.Parent = main

local subtitle = Instance.new("TextLabel")

subtitle.Size =
	UDim2.new(1,-40,0,20)

subtitle.Position =
	UDim2.new(0,20,0,45)

subtitle.BackgroundTransparency = 1

subtitle.Text =
	"Tower Farming System"

subtitle.TextColor3 =
	Color3.fromRGB(145,145,165)

subtitle.TextSize = 12

subtitle.Font =
	Enum.Font.Gotham

subtitle.TextXAlignment =
	Enum.TextXAlignment.Left

subtitle.Parent = main

--==================================================
-- MINIMIZE
--==================================================

local mini = Instance.new("TextButton")

mini.Size =
	UDim2.new(0,38,0,38)

mini.Position =
	UDim2.new(1,-48,0,10)

mini.BackgroundColor3 =
	Color3.fromRGB(35,35,48)

mini.Text = "—"

mini.TextColor3 =
	Color3.fromRGB(255,255,255)

mini.TextSize = 20

mini.Font =
	Enum.Font.GothamBold

mini.Parent = main

local miniCorner =
	Instance.new("UICorner")

miniCorner.CornerRadius =
	UDim.new(0,10)

miniCorner.Parent = mini

--==================================================
-- STATUS
--==================================================

local status =
	Instance.new("TextLabel")

status.Size =
	UDim2.new(1,-40,0,45)

status.Position =
	UDim2.new(0,20,0,75)

status.BackgroundColor3 =
	Color3.fromRGB(27,27,38)

status.Text =
	"● READY"

status.TextColor3 =
	Color3.fromRGB(100,255,160)

status.TextSize = 14

status.Font =
	Enum.Font.GothamBold

status.Parent = main

local statusCorner =
	Instance.new("UICorner")

statusCorner.CornerRadius =
	UDim.new(0,12)

statusCorner.Parent =
	status

--==================================================
-- BUTTON CREATOR
--==================================================

local function makeButton(text,y)

	local button =
		Instance.new("TextButton")

	button.Size =
		UDim2.new(1,-40,0,48)

	button.Position =
		UDim2.new(0,20,0,y)

	button.BackgroundColor3 =
		Color3.fromRGB(35,35,48)

	button.Text = text

	button.TextColor3 =
		Color3.fromRGB(240,240,245)

	button.TextSize = 14

	button.Font =
		Enum.Font.GothamBold

	button.AutoButtonColor = true

	button.Parent = main

	local c =
		Instance.new("UICorner")

	c.CornerRadius =
		UDim.new(0,12)

	c.Parent = button

	return button
end

--==================================================
-- BUTTONS
--==================================================

local farmButton =
	makeButton(
		"⚡ AUTO FARM : OFF",
		130
	)

local stopButton =
	makeButton(
		"■ STOP FARM",
		185
	)

local afkButton =
	makeButton(
		"◉ ANTI AFK : OFF",
		240
	)

local interactButton =
	makeButton(
		"⚡ AUTO INTERACT : OFF",
		295
	)

--==================================================
-- TWEEN TIME
--==================================================

local speedBox =
	Instance.new("TextBox")

speedBox.Size =
	UDim2.new(1,-40,0,45)

speedBox.Position =
	UDim2.new(0,20,0,350)

speedBox.BackgroundColor3 =
	Color3.fromRGB(27,27,38)

speedBox.Text = "2"

speedBox.PlaceholderText =
	"Tween Time"

speedBox.TextColor3 =
	Color3.fromRGB(255,255,255)

speedBox.PlaceholderColor3 =
	Color3.fromRGB(120,120,140)

speedBox.TextSize = 14

speedBox.Font =
	Enum.Font.Gotham

speedBox.ClearTextOnFocus = false

speedBox.Parent = main

local speedCorner =
	Instance.new("UICorner")

speedCorner.CornerRadius =
	UDim.new(0,12)

speedCorner.Parent =
	speedBox

local info =
	Instance.new("TextLabel")

info.Size =
	UDim2.new(1,-40,0,25)

info.Position =
	UDim2.new(0,20,0,405)

info.BackgroundTransparency = 1

info.Text =
	"4 WAYPOINTS • CHECK 5 MIN"

info.TextColor3 =
	Color3.fromRGB(110,110,130)

info.TextSize = 11

info.Font =
	Enum.Font.Gotham

info.Parent = main

--==================================================
-- TWEEN
--==================================================

local function tweenTo(position)

	local char,hrp =
		getCharacter()

	if not hrp then
		return
	end

	local time =
		tonumber(speedBox.Text) or 2

	time =
		math.clamp(time,0.2,10)

	local tweenInfo =
		TweenInfo.new(
			time,
			Enum.EasingStyle.Linear,
			Enum.EasingDirection.InOut
		)

	currentTween =
		TweenService:Create(
			hrp,
			tweenInfo,
			{
				CFrame =
					CFrame.new(position)
			}
		)

	currentTween:Play()

	currentTween.Completed:Wait()

	currentTween = nil
end

--==================================================
-- CHECK INTERACT
--==================================================

local function hasInteractPrompt()

	local char =
		player.Character

	local hrp =
		char and
		char:FindFirstChild(
			"HumanoidRootPart"
		)

	if not hrp then
		return false
	end

	for _,obj in ipairs(
		workspace:GetDescendants()
	) do

		if obj:IsA("ProximityPrompt")
		and obj.Enabled then

			local parent =
				obj.Parent

			if parent
			and parent:IsA("BasePart") then

				local distance =
					(
						hrp.Position
						- parent.Position
					).Magnitude

				if distance <=
					obj.MaxActivationDistance then

					return true
				end
			end
		end
	end

	return false
end

--==================================================
-- FALLBACK TELEPORT
--==================================================

local function fallbackTeleport()

	local char =
		player.Character

	local hrp =
		char and
		char:FindFirstChild(
			"HumanoidRootPart"
		)

	if hrp then

		hrp.CFrame =
			CFrame.new(
				fallbackPosition
			)

	end
end

--==================================================
-- 5 MINUTE CHECK
--==================================================

local function startInteractChecker()

	task.spawn(function()

		while autoFarm do

			task.wait(CHECK_INTERVAL)

			if not autoFarm then
				break
			end

			status.Text =
				"● CHECKING INTERACT..."

			local found =
				hasInteractPrompt()

			if not found then

				status.Text =
					"● INTERACT NOT FOUND"

				task.wait(0.5)

				fallbackTeleport()

				status.Text =
					"● FALLBACK TELEPORT"

				task.wait(1)

			end
		end

	end)
end

--==================================================
-- AUTO FARM
--==================================================

local function stopAutoFarm()

	autoFarm = false

	if currentTween then

		currentTween:Cancel()
		currentTween = nil

	end

	farmButton.Text =
		"⚡ AUTO FARM : OFF"

	farmButton.TextColor3 =
		Color3.fromRGB(
			240,240,245
		)

	status.Text =
		"● AUTO FARM : OFF"

	status.TextColor3 =
		Color3.fromRGB(
			255,180,100
		)
end

local function startAutoFarm()

	if autoFarm then
		return
	end

	autoFarm = true

	farmButton.Text =
		"⚡ AUTO FARM : ON"

	farmButton.TextColor3 =
		Color3.fromRGB(
			100,255,160
		)

	startInteractChecker()

	task.spawn(function()

		while autoFarm do

			for i,position in ipairs(points) do

				if not autoFarm then
					break
				end

				status.Text =
					"● FARMING "..i.."/4"

				tweenTo(position)

				if not autoFarm then
					break
				end

				task.wait(0.2)
			end

			-- LANGSUNG BALIK KE WAYPOINT 1
			-- TIDAK ADA COOLDOWN 5 MENIT

		end

	end)
end

farmButton.MouseButton1Click:Connect(function()

	if autoFarm then
		stopAutoFarm()
	else
		startAutoFarm()
	end

end)

stopButton.MouseButton1Click:Connect(
	stopAutoFarm
)

--==================================================
-- AUTO INTERACT
--==================================================

local function autoInteractLoop()

	task.spawn(function()

		while autoInteract do

			task.wait(0.15)

			local char =
				player.Character

			local hrp =
				char and
				char:FindFirstChild(
					"HumanoidRootPart"
				)

			if not hrp then
				continue
			end

			for _,obj in ipairs(
				workspace:GetDescendants()
			) do

				if not autoInteract then
					break
				end

				if obj:IsA(
					"ProximityPrompt"
				)
				and obj.Enabled then

					local parent =
						obj.Parent

					if parent
					and parent:IsA(
						"BasePart"
					) then

						local distance =
							(
								hrp.Position
								- parent.Position
							).Magnitude

						if distance <=
							obj.MaxActivationDistance then

							obj:InputHoldBegin()

							if obj.HoldDuration > 0 then

								task.wait(
									obj.HoldDuration
								)

							end

							obj:InputHoldEnd()

						end
					end
				end
			end
		end

	end)
end

interactButton.MouseButton1Click:Connect(function()

	autoInteract =
		not autoInteract

	if autoInteract then

		interactButton.Text =
			"⚡ AUTO INTERACT : ON"

		interactButton.TextColor3 =
			Color3.fromRGB(
				100,255,160
			)

		autoInteractLoop()

	else

		interactButton.Text =
			"⚡ AUTO INTERACT : OFF"

		interactButton.TextColor3 =
			Color3.fromRGB(
				240,240,245
			)

	end

end)

--==================================================
-- ANTI AFK
--==================================================

local function antiAFKLoop()

	task.spawn(function()

		while antiAFK do

			task.wait(60)

			if not antiAFK then
				break
			end

			local char =
				player.Character

			local humanoid =
				char and
				char:FindFirstChildOfClass(
					"Humanoid"
				)

			if humanoid then

				humanoid:Move(
					Vector3.new(1,0,0),
					false
				)

				task.wait(0.4)

				humanoid:Move(
					Vector3.new(-1,0,0),
					false
				)

				task.wait(0.4)

				humanoid:Move(
					Vector3.zero,
					false
				)

			end
		end

	end)
end

afkButton.MouseButton1Click:Connect(function()

	antiAFK =
		not antiAFK

	if antiAFK then

		afkButton.Text =
			"◉ ANTI AFK : ON"

		afkButton.TextColor3 =
			Color3.fromRGB(
				100,255,160
			)

		antiAFKLoop()

	else

		afkButton.Text =
			"◉ ANTI AFK : OFF"

		afkButton.TextColor3 =
			Color3.fromRGB(
				240,240,245
			)

	end

end)

--==================================================
-- DRAG MOBILE + PC
--==================================================

local dragging = false
local dragStart
local startPosition

title.InputBegan:Connect(function(input)

	if input.UserInputType ==
		Enum.UserInputType.MouseButton1
	or input.UserInputType ==
		Enum.UserInputType.Touch then

		dragging = true

		dragStart =
			input.Position

		startPosition =
			main.Position

		input.Changed:Connect(function()

			if input.UserInputState ==
				Enum.UserInputState.End then

				dragging = false

			end

		end)

	end

end)

UserInputService.InputChanged:Connect(function(input)

	if not dragging then
		return
	end

	if input.UserInputType ==
		Enum.UserInputType.MouseMovement
	or input.UserInputType ==
		Enum.UserInputType.Touch then

		local delta =
			input.Position -
			dragStart

		main.Position =
			UDim2.new(
				startPosition.X.Scale,
				startPosition.X.Offset
					+ delta.X,

				startPosition.Y.Scale,
				startPosition.Y.Offset
					+ delta.Y
			)

	end

end)

--==================================================
-- MINIMIZE
--==================================================

local minimized = false

mini.MouseButton1Click:Connect(function()

	minimized =
		not minimized

	for _,obj in ipairs(
		main:GetChildren()
	) do

		if obj ~= title
		and obj ~= subtitle
		and obj ~= mini
		and obj:IsA("GuiObject") then

			obj.Visible =
				not minimized

		end
	end

	if minimized then

		main.Size =
			UDim2.new(0,350,0,65)

		mini.Text = "+"

	else

		main.Size =
			UDim2.new(0,350,0,455)

		mini.Text = "—"

	end

end)
