-- loadstring을 사용한 스크립트
local explosionSoundId = "rbxassetid://9064887432"
local damageAmount = 100
local explosionOccurred = false  -- 폭발이 이미 일어난 경우를 추적하는 변수

-- 폭발을 발생시키는 함수
local function createExplosion(explosionPosition, excludePlayer)
    -- 이미 폭발이 일어났다면 더 이상 실행되지 않도록 합니다.
    if explosionOccurred then
        return
    end
    explosionOccurred = true  -- 폭발이 일어났음을 표시

    -- 서버에서 모든 플레이어를 순회합니다.
    for _, player in pairs(game.Players:GetPlayers()) do
        if player.Character and player.Character:FindFirstChild("Humanoid") then
            -- 특정 플레이어를 제외하고 나머지 플레이어에게 폭발을 적용합니다.
            if player ~= excludePlayer then
                local humanoid = player.Character.Humanoid
                -- 폭발이 플레이어의 머리로 영향을 미친다고 가정
                local head = player.Character:FindFirstChild("Head")
                if head then
                    -- 폭발 데미지
                    humanoid:TakeDamage(damageAmount)
                end
            end
        end
    end
    
    -- 오디오를 추가하고 재생합니다.
    local explosionSound = Instance.new("Sound")
    explosionSound.SoundId = explosionSoundId
    explosionSound.Parent = workspace
    explosionSound.Position = explosionPosition
    explosionSound:Play()

    -- 폭발 효과 생성
    local explosion = Instance.new("Explosion")
    explosion.Position = explosionPosition
    explosion.BlastRadius = 10
    explosion.Parent = workspace
end

-- loadstring을 사용하여 스크립트를 실행하는 부분
local scriptContent = [[
    -- 즉시 폭발을 발생시키도록 설정 (예시로 첫 번째 플레이어의 캐릭터에서)
    game.Players.PlayerAdded:Connect(function(player)
        player.CharacterAdded:Connect(function(character)
            if not explosionOccurred then
                -- 1초 후에 폭발이 발생하도록 설정
                wait(1)  -- 1초 후에 폭발이 발생하도록 설정
                createExplosion(character.HumanoidRootPart.Position, player)
            end
        end)
    end)
]]

-- loadstring을 사용하여 스크립트를 실행합니다.
loadstring(scriptContent)()
