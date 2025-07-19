local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()

local Window = WindUI:CreateWindow({
    Title = "Mushyo HUB", -- UI Title
    Icon = "podcast", -- Url or rbxassetid or lucide
    Author = "lk_.12", -- Author & Creator
    Folder = "Mushyo hub", -- Folder name for saving data (And key)
    Size = UDim2.fromOffset(580, 460), 
    Transparent = true,-- UI Transparency
    Theme = "Dark", -- UI Theme
    SideBarWidth = 170, -- UI Sidebar Width (number)
    HasOutline = true, -- Adds Oultines to the window
})

local Tabs = {
    HomeTab = Window:Tab({ Title = "Home", Icon = "house", Desc = "Gerencie opções do servidor." }),
    TargetTab = Window:Tab({ Title = "Target", Icon = "circle-user-round", Desc = "Selecione e faça o que quiser com a pessoa." }),
    CharacterTab = Window:Tab({ Title = "Character", Icon = "person-standing", Desc = "Modifique seu personagem." }),
    Misc = Window:Tab({Title = "Misc", Icon = "box", Desc = "Funções extras."}),
    Exploits = Window:Tab({Title = "Exploits", Icon = "bug", Desc = "Funções de exploits."}),
    b = Window:Divider(),
    WindowTab = Window:Tab({ Title = "Settings", Icon = "settings", Desc = "Gerenciar configurações do HUB." }),
    CreateThemeTab = Window:Tab({ Title = "Theme", Icon = "palette", Desc = "Crie e aplique temas personalizados." }),
}

Window:SelectTab(1)

-- Estatísticas do jogador (exemplo, substitua pelos valores reais)
local player = game.Players.LocalPlayer
local minutos = 0
local minutosParagraph
if player:FindFirstChild("leaderstats") and player.leaderstats:FindFirstChild("Minutes") then
    minutos = player.leaderstats.Minutes.Value
    minutosParagraph = Tabs.HomeTab:Paragraph({
        Title = "Estatísticas",
        Desc = "Nome: " .. player.Name .. "\nMinutos: " .. tostring(minutos)
    })
    player.leaderstats.Minutes.Changed:Connect(function(newValue)
        minutosParagraph:SetDesc("Nome: " .. player.Name .. "\nMinutos: " .. tostring(newValue))
    end)
else
    minutosParagraph = Tabs.HomeTab:Paragraph({
        Title = "Estatísticas",
        Desc = "Nome: " .. player.Name .. "\nMinutos: N/A"
    })
end

-- Estatísticas do servidor
local Players = game:GetService("Players")
local statsParagraph = Tabs.HomeTab:Paragraph({
    Title = "Estatísticas do Servidor",
    Desc = "Jogadores: " .. tostring(#Players:GetPlayers()) ..
           "\nMáximo: " .. tostring(Players.MaxPlayers or "??") ..
           "\nJobId: " .. tostring(game.JobId) ..
           "\nPlaceId: " .. tostring(game.PlaceId)
})

-- Atualizar estatísticas em tempo real
Players.PlayerAdded:Connect(function()
    statsParagraph:SetDesc(
        "Jogadores: " .. tostring(#Players:GetPlayers()) ..
        "\nMáximo: " .. tostring(Players.MaxPlayers or "??") ..
        "\nJobId: " .. tostring(game.JobId) ..
        "\nPlaceId: " .. tostring(game.PlaceId)
    )
end)
Players.PlayerRemoving:Connect(function(player)
    statsParagraph:SetDesc(
        "Jogadores: " .. tostring(#Players:GetPlayers()) ..
        "\nMáximo: " .. tostring(Players.MaxPlayers or "??") ..
        "\nJobId: " .. tostring(game.JobId) ..
        "\nPlaceId: " .. tostring(game.PlaceId)
    )
end)

local function copyCurrentJobId()
    -- Pega o Job ID do servidor atual
    local currentJobId = game.JobId

    -- A função setclipboard() é comum em executores de script
    if setclipboard then
        setclipboard(currentJobId)
        print("Job ID copiado para a área de transferência: " .. currentJobId)
        
        -- Você pode adicionar uma notificação aqui, se sua UI tiver suporte.
        -- Ex: Library.Notify("Job ID copiado com sucesso!")
    else
        warn("A função 'setclipboard' não está disponível neste ambiente.")
    end
end

-- Seu botão, agora com a funcionalidade de copiar
local Button = Tabs.HomeTab:Button({
 Title = "Copiar Job ID",
 Desc = "Copia o ID deste servidor",
 Locked = false,
 Callback = copyCurrentJobId -- Atribui a função de cópia ao callback do botão
})

local TeleportService = game:GetService("TeleportService")
local Players = game:GetService("Players")
local localPlayer = Players.LocalPlayer

-- ID do Jogo (Place ID)
local targetPlaceId = 17274762379

-- Sua função de input, agora com a lógica de teleporte
local Input = Tabs.HomeTab:Input({
 Title = "Entrar por Job ID",
 Desc = "Cola o ID do servidor (Job ID) para entrar.",
 InputIcon = "gamepad", -- Ícone alterado para algo mais relevante
 Type = "Input",
 Placeholder = "Cole o Job ID aqui...",
 Callback = function(jobId) -- A variável 'input' agora é 'jobId' para clareza
    if jobId and jobId ~= "" then
            print("Iniciando teleporte para o servidor: " .. jobId)
            
            -- Tenta realizar o teleporte de forma segura
            local success, errorMessage = pcall(function()
                TeleportService:TeleportToPlaceInstance(targetPlaceId, jobId, localPlayer)
            end)

            if not success then
                warn("Falha no teleporte: " .. errorMessage)
            end
        else
            print("Job ID inválido ou vazio.")
        end
 end
})

-- Seção de opções do servidor
Tabs.HomeTab:Section({ Title = "Opções de Servidor" })

Tabs.HomeTab:Button({
    Title = "Deixar de Dia",
    Desc = "Muda o tempo para dia.",
    Callback = function()
        local Lighting = game:GetService("Lighting")
        Lighting.ClockTime = 12
    end,
})

Tabs.HomeTab:Button({
    Title = "Deixar de Noite",
    Desc = "Muda o tempo para noite.",
    Callback = function()
        local Lighting = game:GetService("Lighting")
        Lighting.ClockTime = 0
    end,
})

Tabs.HomeTab:Button({
    Title = "Reentrar no Servidor",
    Desc = "Reconecta ao servidor atual.",
    Callback = function()
        game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, game.JobId)
    end,
})

Tabs.HomeTab:Button({
    Title = "Server Hop (Vazio)",
    Desc = "Vai para um servidor vazio.",
    Callback = function()
        local HttpService = game:GetService("HttpService")
        local TeleportService = game:GetService("TeleportService")
        local PlaceId = 17274762379
        local url = ("https://games.roblox.com/v1/games/%d/servers/Public?sortOrder=Asc&limit=100"):format(PlaceId)
        local success, response = pcall(function()
            return HttpService:JSONDecode(game:HttpGet(url))
        end)
        if success and response and response.data then
            for _, server in ipairs(response.data) do
                if server.playing < server.maxPlayers then
                    if server.id ~= game.JobId then
                        TeleportService:TeleportToPlaceInstance(PlaceId, server.id)
                        return
                    end
                end
            end
        end
        -- Se não encontrar, recarrega o servidor atual
        TeleportService:Teleport(PlaceId)
    end,
})

local plr = Players.LocalPlayer
local TargetedPlayer = nil
local ForceWhitelist = ForceWhitelist or {}
local ScriptWhitelist = ScriptWhitelist or {}

-- Variáveis adicionais para o sistema de Target
local Velocity_Asset
pcall(function()
    -- Cria um objeto BodyVelocity para controlar movimento em ações
    Velocity_Asset = Instance.new("BodyVelocity")
    Velocity_Asset.Name = "BreakVelocity"
    Velocity_Asset.MaxForce = Vector3.new(100000, 100000, 100000)
    Velocity_Asset.Velocity = Vector3.new(0, 0, 0)
end)

-- Função para animar o personagem
local function PlayAnim(id, time, speed)
    pcall(function()
        if not plr.Character or not plr.Character:FindFirstChild("Humanoid") then
            WindUI:Notify({
                Title = "Erro",
                Content = "Seu personagem não está pronto para animar.",
                Duration = 2
            })
            return
        end
        
        plr.Character.Animate.Disabled = false
        local hum = plr.Character.Humanoid
        local animtrack = hum:GetPlayingAnimationTracks()
        for i, track in pairs(animtrack) do
            track:Stop()
        end
        plr.Character.Animate.Disabled = true
        
        local Anim = Instance.new("Animation")
        Anim.AnimationId = "rbxassetid://"..id
        local loadanim = hum:LoadAnimation(Anim)
        loadanim:Play()
        if time then 
            loadanim.TimePosition = time
        end
        if speed then
            loadanim:AdjustSpeed(speed)
        end
        
        loadanim.Stopped:Connect(function()
            plr.Character.Animate.Disabled = false
            for i, track in pairs(animtrack) do
                track:Stop()
            end
        end)
        
        _G.CurrentAnimation = loadanim
    end)
end

-- Função para parar a animação atual
local function StopAnim()
    pcall(function()
        if plr.Character and plr.Character:FindFirstChild("Humanoid") then
            plr.Character.Animate.Disabled = false
            local animtrack = plr.Character.Humanoid:GetPlayingAnimationTracks()
            for i, track in pairs(animtrack) do
                track:Stop()
            end
        end
        
        _G.CurrentAnimation = nil
    end)
end

-- Função para obter o ping do jogador
local function GetPing()
    local ping = 0
    pcall(function()
        ping = game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue() / 1000
    end)
    return ping or 0.2
end

-- Função para obter a ferramenta Push
local function GetPush()
    for _, tool in ipairs(plr.Backpack:GetChildren()) do
        if tool.Name == "Push" or tool.Name == "ModdedPush" then
            return tool
        end
    end
    for _, tool in ipairs(plr.Character:GetChildren()) do
        if tool.Name == "Push" or tool.Name == "ModdedPush" then
            return tool
        end
    end
    return nil
end

-- Função para obter jogador pelo nome/display
local function GetPlayer(UserDisplay)
    if UserDisplay and UserDisplay ~= "" then
        for i,v in pairs(Players:GetPlayers()) do
            if v.Name:lower():match(UserDisplay:lower()) or v.DisplayName:lower():match(UserDisplay:lower()) then
                return v
            end
        end
    end
    return nil
end

-- Funções auxiliares Target
local function GetCharacter(Player)
    return Player and Player.Character or nil
end

local function GetRoot(Player)
    local char = GetCharacter(Player)
    if char and char:FindFirstChild("HumanoidRootPart") then
        return char.HumanoidRootPart
    end
    return nil
end

local function TeleportTO(posX,posY,posZ,targetPlayer,method)
    pcall(function()
        local localRoot = GetRoot(plr)
        if not localRoot then return end

        if method == "safe" then
            task.spawn(function()
                for i = 1,30 do
                    task.wait()
                    if localRoot then
                        localRoot.Velocity = Vector3.new(0,0,0)
                        if targetPlayer == "pos" then
                            localRoot.CFrame = CFrame.new(posX,posY,posZ)
                        else
                            local targetRoot = GetRoot(targetPlayer)
                            if targetRoot then
                                localRoot.CFrame = CFrame.new(targetRoot.Position) + Vector3.new(0,2,0)
                            end
                        end
                    end
                end
            end)
        else
            if localRoot then
                localRoot.Velocity = Vector3.new(0,0,0)
                if targetPlayer == "pos" then
                    localRoot.CFrame = CFrame.new(posX,posY,posZ)
                else
                    local targetRoot = GetRoot(targetPlayer)
                    if targetRoot then
                        localRoot.CFrame = CFrame.new(targetRoot.Position) + Vector3.new(0,2,0)
                    end
                end
            end
        end
    end)
end

local function PredictionTP(targetPlayer,method)
    pcall(function()
        local localRoot = GetRoot(plr)
        local targetRoot = GetRoot(targetPlayer)
        if not localRoot or not targetRoot then return end

        local pos = targetRoot.Position
        local vel = targetRoot.Velocity
        local ping = GetPing()

        localRoot.CFrame = CFrame.new(
            (pos.X) + (vel.X) * (ping * 3.5),
            (pos.Y) + (vel.Y) * (ping * 2),
            (pos.Z) + (vel.Z) * (ping * 3.5)
        )

        if method == "safe" then
            task.wait()
            localRoot.CFrame = CFrame.new(pos)
            task.wait()
            localRoot.CFrame = CFrame.new(
                (pos.X) + (vel.X) * (ping * 3.5),
                (pos.Y) + (vel.Y) * (ping * 2),
                (pos.Z) + (vel.Z) * (ping * 3.5)
            )
        end
    end)
end

-- Paragraph para feedback
local targetFeedback = Tabs.TargetTab:Paragraph({
    Title = "Status do Alvo",
    Desc = "Nenhum Alvo Selecionado."
})

-- Parágrafo adicional para informações do jogador
local targetInfo = Tabs.TargetTab:Paragraph({
    Title = "Informações do Jogador",
    Desc = "Selecione um alvo para ver informações."
})

-- Botão para criar ferramenta de seleção de alvo
local CreateTargetTool = function()
    -- Remove ferramenta antiga se existir
    if plr.Backpack:FindFirstChild("ClickTarget") then
        plr.Backpack:FindFirstChild("ClickTarget"):Destroy()
    end
    if plr.Character and plr.Character:FindFirstChild("ClickTarget") then
        plr.Character:FindFirstChild("ClickTarget"):Destroy()
    end

    local GetTargetTool = Instance.new("Tool")
    GetTargetTool.Name = "ClickTarget"
    GetTargetTool.RequiresHandle = false
    GetTargetTool.TextureId = "rbxassetid://6043845934" -- ID corrigido
    GetTargetTool.ToolTip = "Selecionar Alvo"
    GetTargetTool.CanBeDropped = false

    GetTargetTool.Activated:Connect(function()
        local mouse = plr:GetMouse()
        local hit = mouse.Target
        local person = nil
        
        if hit and hit.Parent then
            if hit.Parent:IsA("Model") then
                person = Players:GetPlayerFromCharacter(hit.Parent)
            elseif hit.Parent:IsA("Accessory") and hit.Parent.Parent then
                person = Players:GetPlayerFromCharacter(hit.Parent.Parent)
            end
            
            if person and person ~= plr then
                WindUI:Notify({
                    Title = "Alvo Selecionado",
                    Content = "Alvo atual: " .. person.Name,
                    Duration = 2
                })
                
                -- Atualizar variável TargetedPlayer diretamente
                TargetedPlayer = person
                
                -- Atualizar feedback
                targetFeedback:SetTitle("Alvo Selecionado: " .. person.Name)
                targetFeedback:SetDesc("ID: " .. person.UserId .. "\nNome: " .. person.DisplayName)
                
                -- Atualizar informações adicionais do jogador
                local infoText = "Nome: " .. person.Name
                infoText = infoText .. "\nDisplay: " .. person.DisplayName
                infoText = infoText .. "\nUserID: " .. person.UserId
                infoText = infoText .. "\nEntrou: " .. os.date("%d-%m-%Y", os.time() - person.AccountAge * 24 * 3600)
                
                local team = person.Team and person.Team.Name or "Nenhum"
                infoText = infoText .. "\nTime: " .. team
                
                
                targetInfo:SetTitle("Informações: " .. person.Name)
                targetInfo:SetDesc(infoText)
                
                -- Salvar referência global
                _G.TargetedUserId = person.UserId
            elseif person == plr then
                WindUI:Notify({
                    Title = "Erro",
                    Content = "Você não pode selecionar a si mesmo.",
                    Duration = 2
                })
            else
                -- Limpar alvo
                TargetedPlayer = nil
                _G.TargetedUserId = nil
                
                targetFeedback:SetTitle("Status do Alvo")
                targetFeedback:SetDesc("Nenhum Alvo Selecionado.")
                
                targetInfo:SetTitle("Informações do Jogador")
                targetInfo:SetDesc("Selecione um alvo para ver informações.")
                
                WindUI:Notify({
                    Title = "Alvo Removido",
                    Content = "Nenhum jogador selecionado.",
                    Duration = 2
                })
            end
        end
    end)
    
    GetTargetTool.Parent = plr.Backpack
    GetTargetTool.Parent = plr.Character -- Equipar automaticamente a ferramenta
    
    WindUI:Notify({
        Title = "Ferramenta Criada",
        Content = "Use a ferramenta para selecionar um alvo clicando nele.",
        Duration = 3
    })
end

Tabs.TargetTab:Button({
    Title = "Pegar Ferramenta de Seleção",
    Desc = "Cria uma ferramenta para selecionar alvos clicando neles.",
    Icon = "rbxassetid://6043845934",
    Callback = function()
        CreateTargetTool()
    end
})

Tabs.TargetTab:Section({ Title = "Ações no Alvo" })

-- Botão Visualizar Alvo - Converter para Toggle
Tabs.TargetTab:Toggle({
    Title = "Visualizar Alvo",
    Desc = "Alterna a câmera para visualizar o alvo.",
    Value = false,
    Callback = function(state)
        if not TargetedPlayer then
            print("Nenhum Alvo Selecionado.")
            return
        end
        
        if state then
            local humanoid = TargetedPlayer.Character and TargetedPlayer.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                workspace.CurrentCamera.CameraSubject = humanoid
                
                WindUI:Notify({
                    Title = "Câmera",
                    Content = "Visualizando " .. TargetedPlayer.Name,
                    Duration = 2
                })
                
                targetFeedback:SetDesc("Visualizando " .. TargetedPlayer.Name)
                
                -- Criar loop para manter a visualização
                _G.ViewLoop = task.spawn(function()
                    while _G.ViewingTarget and TargetedPlayer and task.wait(0.5) do
                        pcall(function()
                            if TargetedPlayer.Character and TargetedPlayer.Character:FindFirstChild("Humanoid") then
                                workspace.CurrentCamera.CameraSubject = TargetedPlayer.Character.Humanoid
                            end
                        end)
                    end
                end)
                
                _G.ViewingTarget = true
            else
                print("Não foi possível encontrar o personagem do alvo.")
            end
        else
            _G.ViewingTarget = false
            
            if _G.ViewLoop then
                task.cancel(_G.ViewLoop)
                _G.ViewLoop = nil
            end
            
            pcall(function()
                workspace.CurrentCamera.CameraSubject = plr.Character.Humanoid
            end)
            
            WindUI:Notify({
                Title = "Câmera",
                Content = "Voltando para visão normal.",
                Duration = 2
            })
            
            targetFeedback:SetDesc("Alvo: " .. TargetedPlayer.Name)
        end
    end
})

-- Botão Focar no Alvo - Converter para Toggle
Tabs.TargetTab:Toggle({
    Title = "Focar no Alvo",
    Desc = "Segue o alvo continuamente.",
    Value = false,
    Callback = function(state)
        if not TargetedPlayer then
            print("Erro: Nenhum Alvo Selecionado.")
            return
        end
        
        if state then
            WindUI:Notify({
                Title = "Foco",
                Content = "Seguindo " .. TargetedPlayer.Name,
                Duration = 2
            })
            
            targetFeedback:SetDesc("Focando em " .. TargetedPlayer.Name)
            
            -- Criar loop para seguir o alvo
            _G.FocusLoop = task.spawn(function()
                _G.FocusingTarget = true
                while _G.FocusingTarget and TargetedPlayer and task.wait(0.2) do
                    pcall(function()
                        TeleportTO(0, 0, 0, TargetedPlayer)
                    end)
                end
            end)
        else
            _G.FocusingTarget = false
            
            if _G.FocusLoop then
                task.cancel(_G.FocusLoop)
                _G.FocusLoop = nil
            end
            
            WindUI:Notify({
                Title = "Foco",
                Content = "Parou de seguir o alvo.",
                Duration = 2
            })
            
            targetFeedback:SetDesc("Alvo: " .. TargetedPlayer.Name)
        end
    end
})

-- Botão Benx no Alvo - Converter para Toggle
Tabs.TargetTab:Toggle({
    Title = "Beng no Alvo",
    Desc = "Come o cu do alvo.",
    Value = false,
    Callback = function(state)
        if not TargetedPlayer then
            print("Erro: Nenhum Alvo Selecionado.")
            return
        end
        
        if state then
            -- Iniciar animação
            PlayAnim(5918726674, 0, 1)
            
            WindUI:Notify({
                Title = "Benx",
                Content = "Executando Benx em " .. TargetedPlayer.Name,
                Duration = 2
            })
            
            targetFeedback:SetDesc("Executando Benx em " .. TargetedPlayer.Name)
            
            -- Criar loop para a posição de Benx
            _G.BenxLoop = task.spawn(function()
                _G.BenxingTarget = true
                while _G.BenxingTarget and TargetedPlayer and task.wait() do
                    pcall(function()
                        local localRoot = GetRoot(plr)
                        local targetRoot = GetRoot(TargetedPlayer)
                        
                        if not localRoot:FindFirstChild("BreakVelocity") then
                            local TempV = Velocity_Asset:Clone()
                            TempV.Parent = localRoot
                        end
                        
                        if localRoot and targetRoot then
                            localRoot.CFrame = targetRoot.CFrame * CFrame.new(0, 0, 1.1) -- Posição frontal exata
                            localRoot.Velocity = Vector3.new(0, 0, 0)
                        end
                    end)
                end
                
                -- Limpar ao terminar
                StopAnim()
                pcall(function()
                    if GetRoot(plr):FindFirstChild("BreakVelocity") then
                        GetRoot(plr).BreakVelocity:Destroy()
                    end
                end)
            end)
        else
            _G.BenxingTarget = false
            
            if _G.BenxLoop then
                task.cancel(_G.BenxLoop)
                _G.BenxLoop = nil
            end
            
            -- Parar animação
            StopAnim()
            pcall(function()
                if GetRoot(plr):FindFirstChild("BreakVelocity") then
                    GetRoot(plr).BreakVelocity:Destroy()
                end
            end)
            
            WindUI:Notify({
                Title = "Benx",
                Content = "Parou de executar Benx.",
                Duration = 2
            })
            
            targetFeedback:SetDesc("Alvo: " .. TargetedPlayer.Name)
        end
    end
})

-- Headsit no Alvo - Converter para Toggle
Tabs.TargetTab:Toggle({
    Title = "Headsit no Alvo",
    Desc = "Senta na cabeça do alvo.",
    Value = false,
    Callback = function(state)
        if not TargetedPlayer then
        print("Erro: Nenhum Alvo Selecionado.")
            return
        end
        
        if state then
            WindUI:Notify({
                Title = "Headsit",
                Content = "Sentando na cabeça de " .. TargetedPlayer.Name,
                Duration = 2
            })
            
            targetFeedback:SetDesc("Headsit em " .. TargetedPlayer.Name)
            
            -- Criar loop para a posição de Headsit
            _G.HeadsitLoop = task.spawn(function()
                _G.HeadsittingTarget = true
                while _G.HeadsittingTarget and TargetedPlayer and task.wait() do
                    pcall(function()
                        local localRoot = GetRoot(plr)
                        local targetHead = TargetedPlayer.Character and TargetedPlayer.Character:FindFirstChild("Head")
                        
                        if not localRoot:FindFirstChild("BreakVelocity") then
                            local TempV = Velocity_Asset:Clone()
                            TempV.Parent = localRoot
                        end
                        
                        if localRoot and targetHead and plr.Character and plr.Character:FindFirstChild("Humanoid") then
                            plr.Character.Humanoid.Sit = true
                            localRoot.CFrame = targetHead.CFrame * CFrame.new(0, 2, 0)
                            localRoot.Velocity = Vector3.new(0, 0, 0)
                        end
                    end)
                end
                
                -- Limpar ao terminar
                pcall(function()
                    if GetRoot(plr):FindFirstChild("BreakVelocity") then
                        GetRoot(plr).BreakVelocity:Destroy()
                    end
                end)
            end)
        else
            _G.HeadsittingTarget = false
            
            if _G.HeadsitLoop then
                task.cancel(_G.HeadsitLoop)
                _G.HeadsitLoop = nil
            end
            
            pcall(function()
                if GetRoot(plr):FindFirstChild("BreakVelocity") then
                    GetRoot(plr).BreakVelocity:Destroy()
                end
            end)
            
            WindUI:Notify({
                Title = "Headsit",
                Content = "Parou de sentar na cabeça do alvo.",
                Duration = 2
            })
            
            targetFeedback:SetDesc("Alvo: " .. TargetedPlayer.Name)
        end
    end
})

-- Stand ao Lado do Alvo - Converter para Toggle
Tabs.TargetTab:Toggle({
    Title = "Stand ao Lado do Alvo",
    Desc = "Fica em pé ao lado do alvo.",
    Value = false,
    Callback = function(state)
        if not TargetedPlayer then
            print("Erro: Nenhum Alvo Selecionado.")
            return
        end
        
        if state then
            -- Iniciar animação de stand
            PlayAnim(13823324057, 4, 0)
            
            WindUI:Notify({
                Title = "Stand",
                Content = "Ficando ao lado de " .. TargetedPlayer.Name,
                Duration = 2
            })
            
            targetFeedback:SetDesc("Stand ao lado de " .. TargetedPlayer.Name)
            
            -- Criar loop para a posição de stand
            _G.StandLoop = task.spawn(function()
                _G.StandingTarget = true
                while _G.StandingTarget and TargetedPlayer and task.wait() do
                    pcall(function()
                        local localRoot = GetRoot(plr)
                        local targetRoot = GetRoot(TargetedPlayer)
                        
                        if not localRoot:FindFirstChild("BreakVelocity") then
                            local TempV = Velocity_Asset:Clone()
                            TempV.Parent = localRoot
                        end
                        
                        if localRoot and targetRoot then
                            localRoot.CFrame = targetRoot.CFrame * CFrame.new(-3, 1, 0) -- Posição lateral exata
                            localRoot.Velocity = Vector3.new(0, 0, 0)
                        end
                    end)
                end
                
                -- Limpar ao terminar
                StopAnim()
                pcall(function()
                    if GetRoot(plr):FindFirstChild("BreakVelocity") then
                        GetRoot(plr).BreakVelocity:Destroy()
                    end
                end)
            end)
        else
            _G.StandingTarget = false
            
            if _G.StandLoop then
                task.cancel(_G.StandLoop)
                _G.StandLoop = nil
            end
            
            -- Parar animação
            StopAnim()
            pcall(function()
                if GetRoot(plr):FindFirstChild("BreakVelocity") then
                    GetRoot(plr).BreakVelocity:Destroy()
                end
            end)
            
            WindUI:Notify({
                Title = "Stand",
                Content = "Parou de ficar ao lado do alvo.",
                Duration = 2
            })
            
            targetFeedback:SetDesc("Alvo: " .. TargetedPlayer.Name)
        end
    end
})

-- Backpack no Alvo - Converter para Toggle
Tabs.TargetTab:Toggle({
    Title = "Backpack no Alvo",
    Desc = "Posição de mochila no alvo.",
    Value = false,
    Callback = function(state)
        if not TargetedPlayer then
            print("Erro: Nenhum Alvo Selecionado.")
            return
        end
        
        if state then
            WindUI:Notify({
                Title = "Backpack",
                Content = "Backpack em " .. TargetedPlayer.Name,
                Duration = 2
            })
            
            targetFeedback:SetDesc("Backpack em " .. TargetedPlayer.Name)
            
            -- Criar loop para a posição de backpack
            _G.BackpackLoop = task.spawn(function()
                _G.BackpackingTarget = true
                while _G.BackpackingTarget and TargetedPlayer and task.wait() do
                    pcall(function()
                        local localRoot = GetRoot(plr)
                        local targetRoot = GetRoot(TargetedPlayer)
                        
                        if not localRoot:FindFirstChild("BreakVelocity") then
                            local TempV = Velocity_Asset:Clone()
                            TempV.Parent = localRoot
                        end
                        
                        if localRoot and targetRoot and plr.Character and plr.Character:FindFirstChild("Humanoid") then
                            plr.Character.Humanoid.Sit = true
                            localRoot.CFrame = targetRoot.CFrame * CFrame.new(0, 0, 1.2) * CFrame.Angles(0, -3, 0)
                            localRoot.Velocity = Vector3.new(0, 0, 0)
                        end
                    end)
                end
                
                -- Limpar ao terminar
                pcall(function()
                    if GetRoot(plr):FindFirstChild("BreakVelocity") then
                        GetRoot(plr).BreakVelocity:Destroy()
                    end
                end)
            end)
        else
            _G.BackpackingTarget = false
            
            if _G.BackpackLoop then
                task.cancel(_G.BackpackLoop)
                _G.BackpackLoop = nil
            end
            
            pcall(function()
                if GetRoot(plr):FindFirstChild("BreakVelocity") then
                    GetRoot(plr).BreakVelocity:Destroy()
                end
            end)
            
            WindUI:Notify({
                Title = "Backpack",
                Content = "Parou de fazer backpack no alvo.",
                Duration = 2
            })
            
            targetFeedback:SetDesc("Alvo: " .. TargetedPlayer.Name)
        end
    end
})

-- Doggy no Alvo - Converter para Toggle
Tabs.TargetTab:Toggle({
    Title = "Doggy no Alvo",
    Desc = "Posição de cachorro no alvo.",
    Value = false,
    Callback = function(state)
        if not TargetedPlayer then
            print("Erro: Nenhum Alvo Selecionado.")
            return
        end
        
        if state then
            -- Iniciar animação de doggy
            PlayAnim(13694096724, 3.4, 0)
            
            WindUI:Notify({
                Title = "Doggy",
                Content = "Doggy em " .. TargetedPlayer.Name,
                Duration = 2
            })
            
            targetFeedback:SetDesc("Doggy em " .. TargetedPlayer.Name)
            
            -- Criar loop para a posição de doggy
            _G.DoggyLoop = task.spawn(function()
                _G.DoggyingTarget = true
                while _G.DoggyingTarget and TargetedPlayer and task.wait() do
                    pcall(function()
                        local localRoot = GetRoot(plr)
                        local targetLowerTorso = nil
                        
                        -- Tentar obter o LowerTorso diretamente
                        if TargetedPlayer.Character and TargetedPlayer.Character:FindFirstChild("LowerTorso") then
                            targetLowerTorso = TargetedPlayer.Character.LowerTorso
                        end
                        
                        if not targetLowerTorso then
                            -- Fallback para o root se LowerTorso não estiver disponível
                            targetLowerTorso = GetRoot(TargetedPlayer)
                        end
                        
                        if not localRoot:FindFirstChild("BreakVelocity") then
                            local TempV = Velocity_Asset:Clone()
                            TempV.Parent = localRoot
                        end
                        
                        if localRoot and targetLowerTorso then
                            localRoot.CFrame = targetLowerTorso.CFrame * CFrame.new(0, 0.23, 0) -- Posição exata do doggy
                            localRoot.Velocity = Vector3.new(0, 0, 0)
                        end
                    end)
                end
                
                -- Limpar ao terminar
                StopAnim()
                pcall(function()
                    if GetRoot(plr):FindFirstChild("BreakVelocity") then
                        GetRoot(plr).BreakVelocity:Destroy()
                    end
                end)
            end)
        else
            _G.DoggyingTarget = false
            
            if _G.DoggyLoop then
                task.cancel(_G.DoggyLoop)
                _G.DoggyLoop = nil
            end
            
            -- Parar animação
            StopAnim()
            pcall(function()
                if GetRoot(plr):FindFirstChild("BreakVelocity") then
                    GetRoot(plr).BreakVelocity:Destroy()
                end
            end)
            
            WindUI:Notify({
                Title = "Doggy",
                Content = "Parou de fazer doggy no alvo.",
                Duration = 2
            })
            
            targetFeedback:SetDesc("Alvo: " .. TargetedPlayer.Name)
        end
    end
})

-- Sugar no Alvo - Nova animação
Tabs.TargetTab:Toggle({
    Title = "Sugar no Alvo",
    Desc = "Faça o alvo te sugar.",
    Value = false,
    Callback = function(state)
        if not TargetedPlayer then
            print("Erro: Nenhum Alvo Selecionado.")
            return
        end
        
        if state then
            -- Usar uma animação de "idle" para manter o personagem reto
            pcall(function()
                if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                    -- Animação de idle/stand
                    PlayAnim(507766666, 0, 0) -- Animação de ficar em pé reto
                    
                    -- Garantir que o personagem não fique inclinado
                    if plr.Character:FindFirstChild("Humanoid") then
                        plr.Character.Humanoid.PlatformStand = true
                    end
                end
            end)
            
            WindUI:Notify({
                Title = "Sugar",
                Content = "Sugar em " .. TargetedPlayer.Name,
                Duration = 2
            })
            
            targetFeedback:SetDesc("Sugar em " .. TargetedPlayer.Name)
            
            -- Variável para controlar a direção do movimento
            local moveDirection = 1
            local moveTimer = 0
            
            -- Criar loop para a posição de sugar
            _G.SugarLoop = task.spawn(function()
                _G.SugaringTarget = true
                while _G.SugaringTarget and TargetedPlayer and task.wait() do
                    pcall(function()
                        local localRoot = GetRoot(plr)
                        local targetHead = nil
                        
                        -- Tentar obter a Head diretamente
                        if TargetedPlayer.Character and TargetedPlayer.Character:FindFirstChild("Head") then
                            targetHead = TargetedPlayer.Character.Head
                        end
                        
                        if not targetHead then
                            -- Fallback para o root se Head não estiver disponível
                            targetHead = GetRoot(TargetedPlayer)
                        end
                        
                        if not localRoot:FindFirstChild("BreakVelocity") then
                            local TempV = Velocity_Asset:Clone()
                            TempV.Parent = localRoot
                        end
                        
                        -- Calcular o offset do movimento para frente e para trás
                        moveTimer = moveTimer + 0.1
                        if moveTimer > 1 then
                            moveDirection = -moveDirection
                            moveTimer = 0
                        end
                        
                        -- Offset adicional para o movimento para frente e para trás
                        local offset = 0.3 * moveDirection
                        
                        if localRoot and targetHead then
                            -- Posicionar um pouco acima da altura do rosto, à frente e com o movimento para frente e para trás
                            -- Usando valores negativos no eixo Z para posicionar na frente do rosto
                            -- Adicionando rotação de 180 graus no eixo Y para virar o personagem na direção do alvo
                            -- Valor Y ajustado para ficar mais para cima (0.7)
                            localRoot.CFrame = targetHead.CFrame * CFrame.new(0, 0.7, -(1.5 + offset)) * CFrame.Angles(0, math.rad(180), 0)
                            localRoot.Velocity = Vector3.new(0, 0, 0)
                        end
                    end)
                end
                
                -- Limpar ao terminar
                StopAnim()
                pcall(function()
                    if GetRoot(plr):FindFirstChild("BreakVelocity") then
                        GetRoot(plr).BreakVelocity:Destroy()
                    end
                end)
            end)
        else
            _G.SugaringTarget = false
            
            if _G.SugarLoop then
                task.cancel(_G.SugarLoop)
                _G.SugarLoop = nil
            end
            
            -- Parar animação e restaurar estado normal do personagem
            StopAnim()
            pcall(function()
                if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                    plr.Character.Humanoid.PlatformStand = false
                end
                
                if GetRoot(plr):FindFirstChild("BreakVelocity") then
                    GetRoot(plr).BreakVelocity:Destroy()
                end
            end)
            
            WindUI:Notify({
                Title = "Sugar",
                Content = "Parou de fazer o alvo te sugar",
                Duration = 2
            })
            
            targetFeedback:SetDesc("Alvo: " .. TargetedPlayer.Name)
        end
    end
})

-- Drag no Alvo - Nova animação
Tabs.TargetTab:Toggle({
    Title = "Drag no Alvo",
    Desc = "Arrasta o alvo pela mão.",
    Value = false,
    Callback = function(state)
        if not TargetedPlayer then
            print("Erro: Nenhum Alvo Selecionado.")
            return
        end
        
        if state then
            -- Usar animação de arrastar
            pcall(function()
                if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                    -- Animação de arrastar (mão estendida)
                    PlayAnim(10714360343, 0.5, 0)
                    
                    -- Garantir que o personagem não fique inclinado
                    if plr.Character:FindFirstChild("Humanoid") then
                        plr.Character.Humanoid.PlatformStand = true
                    end
                end
            end)
            
            WindUI:Notify({
                Title = "Drag",
                Content = "Arrastando " .. TargetedPlayer.Name,
                Duration = 2
            })
            
            targetFeedback:SetDesc("Arrastando " .. TargetedPlayer.Name)
            
            -- Criar loop para a posição de drag
            _G.DragLoop = task.spawn(function()
                _G.DraggingTarget = true
                while _G.DraggingTarget and TargetedPlayer and task.wait() do
                    pcall(function()
                        local localRoot = GetRoot(plr)
                        local targetRightHand = nil
                        
                        -- Tentar obter a RightHand diretamente
                        if TargetedPlayer.Character and TargetedPlayer.Character:FindFirstChild("RightHand") then
                            targetRightHand = TargetedPlayer.Character.RightHand
                        end
                        
                        if not targetRightHand then
                            -- Fallback para o root se RightHand não estiver disponível
                            targetRightHand = GetRoot(TargetedPlayer)
                        end
                        
                        if not localRoot:FindFirstChild("BreakVelocity") then
                            local TempV = Velocity_Asset:Clone()
                            TempV.Parent = localRoot
                        end
                        
                        if localRoot and targetRightHand then
                            -- Posição específica de arrasto
                            localRoot.CFrame = targetRightHand.CFrame * CFrame.new(0, -2.5, 1) * CFrame.Angles(-2, -3, 0)
                            localRoot.Velocity = Vector3.new(0, 0, 0)
                        end
                    end)
                end
                
                -- Limpar ao terminar
                StopAnim()
                pcall(function()
                    if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                        plr.Character.Humanoid.PlatformStand = false
                    end
                    
                    if GetRoot(plr):FindFirstChild("BreakVelocity") then
                        GetRoot(plr).BreakVelocity:Destroy()
                    end
                end)
            end)
        else
            _G.DraggingTarget = false
            
            if _G.DragLoop then
                task.cancel(_G.DragLoop)
                _G.DragLoop = nil
            end
            
            -- Parar animação e restaurar estado normal do personagem
            StopAnim()
            pcall(function()
                if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                    plr.Character.Humanoid.PlatformStand = false
                end
                
                if GetRoot(plr):FindFirstChild("BreakVelocity") then
                    GetRoot(plr).BreakVelocity:Destroy()
                end
            end)
            
            WindUI:Notify({
                Title = "Drag",
                Content = "Parou de arrastar o alvo.",
                Duration = 2
            })
            
            targetFeedback:SetDesc("Alvo: " .. TargetedPlayer.Name)
        end
    end
})

-- Botão Teleportar para o Alvo (sem toggle, ação única)
Tabs.TargetTab:Button({
    Title = "Teleportar para o Alvo",
    Desc = "Teleporta até o alvo (ação única).",
    Callback = function()
        if not TargetedPlayer then
            print("Erro: Nenhum Alvo Selecionado.")
            return
        end
        
        TeleportTO(0, 0, 0, TargetedPlayer, "safe")
        
        WindUI:Notify({
            Title = "Teleporte",
            Content = "Teleportando para " .. TargetedPlayer.Name,
            Duration = 2
        })
        
        targetFeedback:SetDesc("Teleportado para " .. TargetedPlayer.Name)
    end
})

-- Corrigindo problemas de código duplicado no final do arquivo
-- Atualizar quando o alvo sair do jogo (já parece adequada, apenas garantindo limpeza correta)
Players.PlayerRemoving:Connect(function(player)
    pcall(function()
        if TargetedPlayer and player == TargetedPlayer then
            -- Limpar todos os loops ativos
            for _, loopName in ipairs({"ViewLoop", "FocusLoop", "BenxLoop", "HeadsitLoop", "StandLoop", "BackpackLoop", "DoggyLoop", "SugarLoop", "DragLoop"}) do
                if _G[loopName] then
                    task.cancel(_G[loopName])
                    _G[loopName] = nil
                end
            end
            
            -- Limpar estados
            _G.FlingActive = nil
            _G.ViewingTarget = nil
            _G.FocusingTarget = nil
            _G.BenxingTarget = nil
            _G.HeadsittingTarget = nil
            _G.StandingTarget = nil
            _G.BackpackingTarget = nil
            _G.DoggyingTarget = nil
            _G.SugaringTarget = nil
            _G.DraggingTarget = nil
            
            -- Parar animações e limpar efeitos
            StopAnim()
            pcall(function()
                if GetRoot(plr):FindFirstChild("BreakVelocity") then
                    GetRoot(plr).BreakVelocity:Destroy()
                end
                
                workspace.CurrentCamera.CameraSubject = plr.Character.Humanoid
            end)
            
            WindUI:Notify({
                Title = "Alvo Saiu",
                Content = player.Name .. " saiu do jogo.",
                Duration = 3
            })
        end
    end)
end)

-- Implementação da aba Character
Tabs.CharacterTab:Section({ Title = "Velocidade e Movimento" })

-- WalkSpeed
local walkSpeedInput = Tabs.CharacterTab:Input({
    Title = "Velocidade de Caminhada",
    Desc = "Ajuste a velocidade de movimento do seu personagem",
    PlaceholderText = "Valor entre 1-999",
    Value = "16",
    Callback = function(text)
        -- Aplicar a velocidade imediatamente quando o valor mudar
        pcall(function()
            local speed = text:gsub("%D", "")
            if speed == "" then speed = 16 end
            speed = tonumber(speed)
            
            if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                plr.Character.Humanoid.WalkSpeed = speed
                
                WindUI:Notify({
                    Title = "Velocidade",
                    Content = "Velocidade de caminhada atualizada para " .. speed,
                    Duration = 2
                })
            end
        end)
    end
})

-- JumpPower
local jumpPowerInput = Tabs.CharacterTab:Input({
    Title = "Força do Pulo",
    Desc = "Ajuste a altura do pulo do seu personagem",
    PlaceholderText = "Valor entre 1-999",
    Value = "16",
    Callback = function(text)
        -- Aplicar a força do pulo imediatamente quando o valor mudar
        pcall(function()
            local power = text:gsub("%D", "")
            if power == "" then power = 16 end
            power = tonumber(power)
            
            if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                plr.Character.Humanoid.JumpPower = power
                -- Também definir JumpHeight para versões mais recentes do Roblox
                pcall(function() 
                    plr.Character.Humanoid.JumpHeight = power/2
                end)
                
                WindUI:Notify({
                    Title = "Pulo",
                    Content = "Força do pulo atualizada para " .. power,
                    Duration = 2
                })
            end
        end)
    end
})

-- Botão para restaurar valores padrão
Tabs.CharacterTab:Button({
    Title = "Restaurar Valores Padrão",
    Desc = "Volta a velocidade e o pulo para os valores padrão.",
    Callback = function()
        -- 'pcall' foi movido para fora para capturar qualquer erro dentro da função
        local success, err = pcall(function()
            local character = plr and plr.Character
            local humanoid = character and character:FindFirstChildOfClass("Humanoid")

            if humanoid then
                -- Define os valores padrão do Humanoide
                humanoid.WalkSpeed = 16
                humanoid.JumpPower = 16 -- Valor padrão do Roblox para JumpPower
                -- Nota: JumpHeight é calculado a partir do JumpPower. 
                -- O valor padrão de JumpPower é 50, que resulta em uma altura de pulo de 7.2.
                -- Não é recomendado definir os dois ao mesmo tempo. Vamos focar no JumpPower.

                -- Atualiza a interface gráfica (UI) para refletir os valores padrão
                if walkSpeedInput and walkSpeedInput.SetValue then
                    walkSpeedInput:SetValue("16")
                end
                
                if jumpPowerInput and jumpPowerInput.SetValue then
                    -- Atualiza o input para o valor correto do JumpPower
                    jumpPowerInput:SetValue("16") 
                end

                -- Notifica o jogador que os valores foram restaurados
                WindUI:Notify({
                    Title = "Valores Restaurados",
                    Content = "Velocidade e pulo voltaram para os valores padrão.",
                    Duration = 3 -- Aumentei a duração para dar tempo de ler
                })
            else
                -- Notifica caso o personagem ou humanoide não seja encontrado
                WindUI:Notify({
                    Title = "Erro ao Restaurar",
                    Content = "Seu personagem não foi encontrado. Tente novamente.",
                    Duration = 3
                })
            end
        end)

        -- Se ocorrer um erro inesperado dentro do pcall, notifica no console
        if not success then
            warn("Ocorreu um erro ao restaurar os valores:", err)
        end
    end
})

-- FlySpeed
local flySpeedInput = Tabs.CharacterTab:Input({
    Title = "Velocidade de Voo",
    Desc = "Ajuste a velocidade de voo",
    PlaceholderText = "Valor entre 1-999",
    Value = "150",
    Callback = function(text)
        -- Atualizar velocidade de voo imediatamente quando o valor mudar
        pcall(function()
            local speed = text:gsub("%D", "")
            if speed == "" then speed = 150 end
            speed = tonumber(speed)
            
            _G.FlySpeed = speed
            
            WindUI:Notify({
                Title = "Voo",
                Content = "Velocidade de voo atualizada para " .. speed,
                Duration = 2
            })
        end)
    end
})

-- Sistema de voo completo
local flyToggle = Tabs.CharacterTab:Toggle({
    Title = "Modo Voo",
    Desc = "Ativa/desativa o modo de voo",
    Value = false,
    Callback = function(state)
        pcall(function()
            if state then
                -- Iniciar voo
                if not plr.Character or not plr.Character:FindFirstChild("Humanoid") then
                    WindUI:Notify({
                        Title = "Erro",
                        Content = "Seu personagem não está disponível",
                        Duration = 2
                    })
                    flyToggle:SetValue(false)
                    return
                end
                
                _G.Flying = true
                
                -- Animação de voo (pose de flutuação)
                local hum = plr.Character.Humanoid
                local animIds = {
                    idle = "10714347256", -- Animação base (flutuando)
                    moveForward = "10714177846", -- Movimento para frente
                    moveBackward = "10147823318", -- Movimento para trás
                    moveLeft = "10147823318", -- Movimento para esquerda
                    moveRight = "10147823318"  -- Movimento para direita
                }
                
                -- Carrega a animação principal
                PlayAnim(animIds.idle, 0, 0)
                
                -- Configuração física do voo
                local torso = plr.Character:FindFirstChild("UpperTorso") or plr.Character:FindFirstChild("Torso")
                if not torso then
                    WindUI:Notify({
                        Title = "Erro",
                        Content = "Não foi possível encontrar o torso do personagem",
                        Duration = 2
                    })
                    flyToggle:SetValue(false)
                    return
                end
                
                local bg = Instance.new("BodyGyro", torso)
                bg.Name = "FlyGyro"
                bg.P = 9e4
                bg.maxTorque = Vector3.new(9e9, 9e9, 9e9)
                bg.cframe = torso.CFrame
                
                local bv = Instance.new("BodyVelocity", torso)
                bv.Name = "FlyVelocity"
                bv.velocity = Vector3.new(0, 0.1, 0)
                bv.maxForce = Vector3.new(9e9, 9e9, 9e9)
                
                -- Controles de teclado
                local ctrl = {f = 0, b = 0, l = 0, r = 0, q = 0, e = 0}
                local lastCtrl = {f = 0, b = 0, l = 0, r = 0, q = 0, e = 0}
                
                _G.FlyConnection1 = game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
                    if gameProcessed then return end
                    if input.KeyCode == Enum.KeyCode.W then
                        ctrl.f = 1
                        StopAnim()
                        PlayAnim(animIds.moveForward, 0, 0)
                    elseif input.KeyCode == Enum.KeyCode.S then
                        ctrl.b = -1
                        StopAnim()
                        PlayAnim(animIds.moveBackward, 0, 0)
                    elseif input.KeyCode == Enum.KeyCode.A then
                        ctrl.l = -1
                        StopAnim()
                        PlayAnim(animIds.moveLeft, 0, 0)
                    elseif input.KeyCode == Enum.KeyCode.D then
                        ctrl.r = 1
                        StopAnim()
                        PlayAnim(animIds.moveRight, 0, 0)
                    elseif input.KeyCode == Enum.KeyCode.E then
                        ctrl.q = 1
                    elseif input.KeyCode == Enum.KeyCode.Q then
                        ctrl.e = -1
                    end
                end)
                
                _G.FlyConnection2 = game:GetService("UserInputService").InputEnded:Connect(function(input, gameProcessed)
                    if input.KeyCode == Enum.KeyCode.W then
                        ctrl.f = 0
                        StopAnim()
                        PlayAnim(animIds.idle, 0, 0)
                    elseif input.KeyCode == Enum.KeyCode.S then
                        ctrl.b = 0
                        StopAnim()
                        PlayAnim(animIds.idle, 0, 0)
                    elseif input.KeyCode == Enum.KeyCode.A then
                        ctrl.l = 0
                        StopAnim()
                        PlayAnim(animIds.idle, 0, 0)
                    elseif input.KeyCode == Enum.KeyCode.D then
                        ctrl.r = 0
                        StopAnim()
                        PlayAnim(animIds.idle, 0, 0)
                    elseif input.KeyCode == Enum.KeyCode.E then
                        ctrl.q = 0
                    elseif input.KeyCode == Enum.KeyCode.Q then
                        ctrl.e = 0
                    end
                end)
                
                -- Loop de movimento
                _G.FlyLoop = task.spawn(function()
                    while _G.Flying and plr.Character and plr.Character:FindFirstChild("Humanoid") do
                        task.wait()
                        local speed = 0
                        local flySpeed = _G.FlySpeed or 50
                        
                        if (ctrl.l + ctrl.r) ~= 0 or (ctrl.f + ctrl.b) ~= 0 or (ctrl.q + ctrl.e) ~= 0 then
                            speed = math.clamp(speed + flySpeed * 0.1, 0, flySpeed)
                        else
                            speed = math.clamp(speed - flySpeed * 0.1, 0, flySpeed)
                        end
                        
                        local cam = workspace.CurrentCamera.CoordinateFrame
                        local moveDir = (cam.LookVector * (ctrl.f + ctrl.b)) + (cam.RightVector * (ctrl.l + ctrl.r)) + (cam.UpVector * (ctrl.q + ctrl.e))
                        
                        if moveDir.Magnitude > 0 then
                            bv.velocity = moveDir * speed
                            lastCtrl = {f = ctrl.f, b = ctrl.b, l = ctrl.l, r = ctrl.r, q = ctrl.q, e = ctrl.e}
                        else
                            bv.velocity = Vector3.new(0, 0.1, 0)
                        end
                        
                        bg.cframe = cam * CFrame.Angles(-math.rad((ctrl.f + ctrl.b) * 50 * speed/flySpeed), 0, 0)
                    end
                    
                    -- Limpeza ao sair do modo voo (já feita na parte de desativação do voo)
                end)
                
                -- Desabilitar gravidade
                plr.Character.Humanoid.PlatformStand = true
                
                WindUI:Notify({
                    Title = "Voo",
                    Content = "Modo voo ativado. Use WASD para mover, E/Q para subir/descer.",
                    Duration = 3
                })
            else
                -- Parar voo
                _G.Flying = false
                
                if _G.FlyConnection1 then _G.FlyConnection1:Disconnect() end
                if _G.FlyConnection2 then _G.FlyConnection2:Disconnect() end
                if _G.FlyLoop then task.cancel(_G.FlyLoop) end
                
                pcall(function()
                    -- Parar animação
                    StopAnim()
                    
                    -- Restaurar gravidade
                    if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                        plr.Character.Humanoid.PlatformStand = false
                    end
                    
                    -- Remover BodyVelocity e BodyGyro
                    local torso = plr.Character:FindFirstChild("UpperTorso") or plr.Character:FindFirstChild("Torso")
                    if torso then
                        if torso:FindFirstChild("FlyVelocity") then
                            torso.FlyVelocity:Destroy()
                        end
                        if torso:FindFirstChild("FlyGyro") then
                            torso.FlyGyro:Destroy()
                        end
                    end
                end)
                
                WindUI:Notify({
                    Title = "Voo",
                    Content = "Modo voo desativado.",
                    Duration = 2
                })
            end
        end)
    end
})

-- Reinicialização do sistema de voo se o personagem morrer
plr.CharacterAdded:Connect(function()
    if flyToggle and flyToggle:GetValue() then
        flyToggle:SetValue(false)
    end
end)

Tabs.CharacterTab:Section({ Title = "Gerenciamento de Personagem" })

Tabs.CharacterTab:Button({
    Title = "Respawn",
    Desc = "Morre e respawna na mesma posição",
    Callback = function()
        pcall(function()
            local position = GetRoot(plr) and GetRoot(plr).Position
            
            if position then
                -- Matar o personagem
                plr.Character.Humanoid.Health = 0
                
                -- Esperar pelo respawn
                plr.CharacterAdded:Wait()
                task.wait(GetPing() + 0.5)
                
                -- Teleportar de volta
                TeleportTO(position.X, position.Y, position.Z, "pos", "safe")
                
                WindUI:Notify({
                    Title = "Respawn",
                    Content = "Respawn concluído com sucesso.",
                    Duration = 2
                })
            else
                WindUI:Notify({
                    Title = "Erro",
                    Content = "Não foi possível obter a posição atual.",
                    Duration = 2
                })
            end
        end)
    end
})

Tabs.CharacterTab:Button({
    Title = "Teleportar para o Topo",
    Desc = "Teleporta seu personagem para o topo do mapa.",
    Callback = function()
        -- Define a posição alvo
        local posAlvo = Vector3.new(-77, 331, -223)
        
        -- Obtém o jogador local
        local player = game.Players.LocalPlayer
        
        -- Aguarda a existência do personagem e do "HumanoidRootPart"
        local character = player.Character or player.CharacterAdded:Wait()
        local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
        
        -- Teleporta o jogador definindo a CFrame para a nova posição
        humanoidRootPart.CFrame = CFrame.new(posAlvo)
        
        -- Notifica o usuário
        WindUI:Notify({
            Title = "Teleporte",
            Content = "Teleportado para o topo do mapa!",
            Duration = 2
        })
    end
})

-- Misc Buttons
Tabs.Misc:Button({
    Title = "🗣️ Unban Voice Chat",
    Desc = "Click to remove your voice chat ban",
    Callback = function()
        local success, err = pcall(function()
            game:GetService("VoiceChatService"):JoinVoiceChat()
        end)
        
        if success then
            WindUI:Notify({
                Title = "🗣️ Voice Chat Unbanned",
                Content = "Your voice chat has been unbanned!",
                Duration = 1
            })
        else
            WindUI:Notify({
                Title = "Error",
                Content = "Failed to unban voice chat: " .. tostring(err),
                Duration = 1
            })
        end
    end
})

Tabs.Misc:Button({
    Title = "🕳️ Void Player",
    Desc = "Carry a player, activate this, then drop them into the void",
    Callback = function()
        local player = game.Players.LocalPlayer
        local character = player.Character
        
        if not character or not character.PrimaryPart then
            WindUI:Notify({
                Title = "Error",
                Content = "Character not found or invalid!",
                Duration = 1
            })
            return
        end

        local originalPosition = character.PrimaryPart.Position
        local voidPosition = originalPosition - Vector3.new(0, 350, 0)

        WindUI:Notify({
            Title = "🕳️ Void Player",
            Content = "Preparing void teleport...",
            Duration = 1
        })

        character:SetPrimaryPartCFrame(CFrame.new(voidPosition))

        WindUI:Notify({
            Title = "🕳️ Void Player",
            Content = "Player sent to void! Releasing in 3 seconds...",
            Duration = 2
        })

        task.wait(1.3)
        
        if character and character.PrimaryPart then
            character:SetPrimaryPartCFrame(CFrame.new(originalPosition))
            WindUI:Notify({
                Title = "🕳️ Void Player",
                Content = "Player returned from void!",
                Duration = 1
            })
        else
            WindUI:Notify({
                Title = "Error",
                Content = "Character became invalid during process!",
                Duration = 1
            })
        end
    end
})

-- Scripts Tab
Tabs.Misc:Button({
    Title = "📄 Infinity Yield",
    Desc = "Script com varios comandos de adm.",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))()
        WindUI:Notify({
            Title = "📄 Infinity Yield",
            Content = "o infinity yield foi executado!",
            Duration = 1
        })
    end
})

Tabs.Misc:Button({
    Title = "📄 Moon AntiAfk",
    Desc = "Script para não ser kickado por ficar parado.",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/rodri0022/afkmoon/refs/heads/main/README.md', true))()
        WindUI:Notify({
            Title = "📄 Moon AntiAfk",
            Content = "o moon antiafk foi executado!",
            Duration = 1
        })
    end
})

Tabs.Misc:Button({
    Title = "📄 Moon AntiLag",
    Desc = "Script para deixar seu jogo extremamente leve.",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/nick0022/antilag/refs/heads/main/README.md', true))()
        WindUI:Notify({
            Title = "📄 Moon AntiLag",
            Content = "o moon antilag foi executado!",
            Duration = 1
        })
    end
})

Tabs.Misc:Button({
    Title = "📄 Motiona",
    Desc = "Script para mudar suas animações e dança, tudo FE.",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/BeemTZy/Motiona/refs/heads/main/source.lua"))()
        WindUI:Notify({
            Title = "📄 Motiona",
            Content = "o Motiona foi executado!",
            Duration = 1
        })
    end
})

Tabs.Misc:Button({
    Title = "📄 Moon FE Emotes",
    Desc = "Execute the Moon Emotes script.",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/rodri0022/freeanimmoon/refs/heads/main/README.md', true))()
        WindUI:Notify({
            Title = "📄 Moon Emotes",
            Content = "o moon emotes foi executado!",
            Duration = 1
        })
    end
})

Tabs.Misc:Button({
    Title = "📄 Moon Troll",
    Desc = "Script com opções troll como jerk e etc.",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/nick0022/trollscript/refs/heads/main/README.md'))()
        WindUI:Notify({
            Title = "📄 Moon Troll",
            Content = "o moon troll foi executado!",
            Duration = 1
        })
    end
})

Tabs.Misc:Button({
    Title = "📄 Eazvy Animations",
    Desc = "Script com opções troll, danças e etc.",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Eazvy/public-scripts/main/Universal_Animations_Emotes.lua"))()
        WindUI:Notify({
            Title = "📄 Eazvy Animations",
            Content = "o Eazvy Animations foi executado!",
            Duration = 1
        })
    end
})

Tabs.Misc:Button({
    Title = "📄 Sirius",
    Desc = "Script com opções mais normais.",
    Callback = function()
        loadstring(game:HttpGet('https://sirius.menu/sirius'))()
        WindUI:Notify({
            Title = "📄 Sirius",
            Content = "o script Sirius foi executado!",
            Duration = 1
        })
    end
})

Tabs.Misc:Button({
    Title = "📄 Keyboard",
    Desc = "Script de keyboard.",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/GGH52lan/GGH52lan/main/keyboard.txt"))()
        WindUI:Notify({
            Title = "📄 Keyboard",
            Content = "o script keyboard foi executado!",
            Duration = 1
        })
    end
})

Tabs.Misc:Button({
    Title = "📄 Shader",
    Desc = "Script para deixar seu jogo bonito.",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/randomstring0/pshade-ultimate/refs/heads/main/src/cd.lua'))()
        WindUI:Notify({
            Title = "📄 shader",
            Content = "o script shader foi executado!",
            Duration = 1
        })
    end
})

Tabs.Misc:Button({
    Title = "📄 ChatBypass",
    Desc = "Script para deixar você mandar o que quiser no chat. Key: heypew",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/randomizedcomponent/UC/refs/heads/main/3307468c285f4fe535fb5d87b4b053e6.lua"))()
        WindUI:Notify({
            Title = "📄 Chatbypass",
            Content = "o script Chatbypass foi executado!",
            Duration = 1
        })
    end
})

Tabs.Misc:Button({
    Title = "📄 SystemBroken",
    Desc = "Script parecido com esse",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/H20CalibreYT/SystemBroken/main/script"))()
        WindUI:Notify({
            Title = "📄 systembroken",
            Content = "o script systembroken foi executado!",
            Duration = 1
        })
    end
})

--[[
============================================================
Serviços e Variáveis
============================================================
]]
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local localPlayer = Players.LocalPlayer

--[[
============================================================
Controle do Script
============================================================
]]
local autoFishAtivado = false 
local velocidadeOriginal = 16

--[[
============================================================
Interface (Toggle com Teleporte e Paralisia)
============================================================
]]
local autoFishToggle = Tabs.Exploits:Toggle({
    Title = "Auto Fish",
    Desc = "Necessario estar com a vara na mão antes de usar e pesque uma vez antes de usar o auto fish.",
    Icon = "fish",
    Type = "Checkbox",
    Default = false,
    Callback = function(state)
        autoFishAtivado = state
        local personagem = localPlayer.Character
        local humanoid = personagem and personagem:FindFirstChildOfClass("Humanoid")

        if not humanoid then print("[AVISO] Humanoid não encontrado para o callback.") return end

        if autoFishAtivado then
            print("Auto Fish [LIGADO]")
            velocidadeOriginal = humanoid.WalkSpeed
            humanoid.WalkSpeed = 0
            if personagem and personagem:FindFirstChild("HumanoidRootPart") then
                personagem:SetPrimaryPartCFrame(CFrame.new(-130, 31, -108))
            end
        else
            print("Auto Fish [DESLIGADO]")
            humanoid.WalkSpeed = velocidadeOriginal
        end
    end
})

--[[
============================================================
Loop de Pesca (Corrigido para usar a vara na mão)
============================================================
]]
local function iniciarLoopDePesca()
    while task.wait(1) do
        if autoFishAtivado then
            local sucesso, msgErro = pcall(function()
                
                local personagem = localPlayer.Character
                if not personagem then error("Personagem não encontrado.") end

                local varaDePescar = personagem:WaitForChild("Fishing Rod", 5)
                if not varaDePescar then
                    error("FALHA: Não encontrei a 'Fishing Rod' na sua mão.")
                end
                
                local remoteDaVara = varaDePescar:WaitForChild("fishingRemote", 2)
                if not remoteDaVara then
                    error("FALHA: Não encontrei o 'fishingRemote' na sua vara.")
                end

                -- Ação de Lançar (usando a vara que está na sua mão)
                local validateFishing = localPlayer.UserId * game.PlaceVersion * 6 / 2 
                local args_lancar = { { destiny = Vector3.new(-123.32292175292969, 26, -125.62540435791016), fishingAction = "createBuoy", validateFishing = validateFishing } }  
                remoteDaVara:FireServer(unpack(args_lancar))
                
                -- Espera a boia e as bolhas
                local boia = Workspace.Temp:WaitForChild(localPlayer.UserId .. ".buoy", 30)

                -- TEMPO DE ESPERA AJUSTADO PARA 45 SEGUNDOS, COMO SOLICITADO
                boia:WaitForChild("Bubbles", 65)

                if not autoFishAtivado then return end

                -- Ação de Puxar (usando a mesma vara que já está na sua mão)
                -- Este bloco já faz exatamente o que você pediu.
                local args_puxar = { { fishingAction = "fish", validateFishing = validateFishing } } 
                remoteDaVara:FireServer(unpack(args_puxar))
            end)

            if not sucesso and autoFishAtivado then
                warn("ERRO NO LOOP: " .. msgErro)
                task.wait(2)
            end
        end
    end
end

-- Espera o jogo carregar um pouco antes de iniciar o script
task.wait(5)
if Players.LocalPlayer then
    localPlayer = Players.LocalPlayer
    task.spawn(iniciarLoopDePesca)
    print("Script Auto Fish (v.Final) Carregado.")
else
    Players.PlayerAdded:Wait()
    localPlayer = Players.LocalPlayer
    task.spawn(iniciarLoopDePesca)
    print("Script Auto Fish (v.Final - Carregado Atrasado) Carregado.")
end

--[[
============================================================
  WindUI - Script de Configuração (v4 - Correção Final)
============================================================
]]

-- Serviços e Variáveis Globais
local HttpService = game:GetService("HttpService")
local folderPath = "moon_mushyo"
makefolder(folderPath)

--[[
============================================================
  Funções de Arquivo (Salvar, Carregar, Listar)
============================================================
]]
local function SaveFile(fileName, data)
    local filePath = folderPath .. "/" .. fileName .. ".json"
    pcall(function()
        local jsonData = HttpService:JSONEncode(data)
        writefile(filePath, jsonData)
    end)
end

local function LoadFile(fileName)
    local filePath = folderPath .. "/" .. fileName .. ".json"
    if not isfile(filePath) then return nil end
    
    local success, result = pcall(function()
        local jsonData = readfile(filePath)
        return HttpService:JSONDecode(jsonData)
    end)
    
    if success then return result end
    return nil
end

local function ListFiles()
    local files = {}
    local fileList = listfiles(folderPath)
    if not fileList then return files end

    for _, file in ipairs(fileList) do
        local fileName = file:match("([^/]+)%.json$")
        if fileName then
            table.insert(files, fileName)
        end
    end
    return files
end

--[[
============================================================
  Aba: Window (Configurações Gerais)
============================================================
]]
Tabs.WindowTab:Section({ Title = "Window" })

local themeValues = {}
for name, _ in pairs(WindUI:GetThemes()) do
    table.insert(themeValues, name)
end

local themeDropdown 
themeDropdown = Tabs.WindowTab:Dropdown({
    Title = "Select Theme",
    Multi = false,
    AllowNone = false,
    Values = themeValues,
    Callback = function(themeName)
        WindUI:SetTheme(themeName)
        UpdateThemeEditorUI(themeName)
    end
})
themeDropdown:Select(WindUI:GetCurrentTheme())

local ToggleTransparency = Tabs.WindowTab:Toggle({
    Title = "Toggle Window Transparency",
    Callback = function(state)
        Window:ToggleTransparency(state)
    end,
    Value = WindUI:GetTransparency()
})

Tabs.WindowTab:Section({ Title = "Save" })

local saveFileNameInput = ""
Tabs.WindowTab:Input({
    Title = "Write File Name",
    PlaceholderText = "Enter file name to save",
    Callback = function(text)
        saveFileNameInput = text
    end
})

Tabs.WindowTab:Button({
    Title = "Save Config",
    Callback = function()
        if saveFileNameInput ~= "" then
            SaveFile(saveFileNameInput, { Transparent = WindUI:GetTransparency(), Theme = WindUI:GetCurrentTheme() })
            print("Config saved to " .. saveFileNameInput)
            filesDropdown:Refresh(ListFiles())
        end
    end
})

Tabs.WindowTab:Section({ Title = "Load" })

local loadFileNameSelected = ""
local filesDropdown
filesDropdown = Tabs.WindowTab:Dropdown({
    Title = "Select File to Load",
    Multi = false,
    AllowNone = true,
    Values = ListFiles(),
    Callback = function(selectedFile)
        loadFileNameSelected = selectedFile
    end
})

Tabs.WindowTab:Button({
    Title = "Load Config",
    Callback = function()
        if loadFileNameSelected ~= "" then
            local data = LoadFile(loadFileNameSelected)
            if data then
                if data.Transparent ~= nil then 
                    Window:ToggleTransparency(data.Transparent)
                    ToggleTransparency:SetValue(data.Transparent)
                end
                if data.Theme then 
                    WindUI:SetTheme(data.Theme)
                    themeDropdown:Select(data.Theme)
                    UpdateThemeEditorUI(data.Theme)
                end
                WindUI:Notify({ Title = "Config Loaded", Content = "Loaded file: " .. loadFileNameSelected, Duration = 5 })
            end
        end
    end
})

Tabs.WindowTab:Button({
    Title = "Refresh List",
    Callback = function()
        filesDropdown:Refresh(ListFiles())
    end
})

--[[
============================================================
  Aba: Create Theme (Editor de Temas)
============================================================
]]

local function safeColor(hex)
    return (type(hex) == "string" and hex:match("^#?%x%x%x%x%x%x$")) and hex or "#FFFFFF"
end

local themeNameInput = Tabs.CreateThemeTab:Input({ Title = "Theme Name" })
local accentColorpicker = Tabs.CreateThemeTab:Colorpicker({ Title = "Background Color" })
local outlineColorpicker = Tabs.CreateThemeTab:Colorpicker({ Title = "Outline Color" })
local textColorpicker = Tabs.CreateThemeTab:Colorpicker({ Title = "Text Color" })
local placeholderColorpicker = Tabs.CreateThemeTab:Colorpicker({ Title = "Placeholder Text Color" })

function UpdateThemeEditorUI(themeName)
    local themes = WindUI:GetThemes()
    local themeData = themes[themeName] or {}

    -- Define o valor do campo de texto diretamente
    themeNameInput.Value = themeName

    -- ==========================================================
    -- CORREÇÃO FINAL APLICADA AQUI EM TODOS OS COLORPICKERS
    -- ==========================================================
    accentColorpicker.Value = Color3.fromHex(safeColor(themeData.Accent))
    outlineColorpicker.Value = Color3.fromHex(safeColor(themeData.Outline))
    textColorpicker.Value = Color3.fromHex(safeColor(themeData.Text))
    placeholderColorpicker.Value = Color3.fromHex(safeColor(themeData.PlaceholderText))
end

Tabs.CreateThemeTab:Button({
    Title = "Save/Update Theme",
    Callback = function()
        local newThemeData = {
            Name = themeNameInput.Value,
            Accent = accentColorpicker.Value:ToHex(),
            Outline = outlineColorpicker.Value:ToHex(),
            Text = textColorpicker.Value:ToHex(),
            PlaceholderText = placeholderColorpicker.Value:ToHex()
        }
        
        WindUI:AddTheme(newThemeData)
        WindUI:SetTheme(newThemeData.Name)
        
        local newThemeValues = {}
        for name, _ in pairs(WindUI:GetThemes()) do table.insert(newThemeValues, name) end
        themeDropdown:Refresh(newThemeValues)
        themeDropdown:Select(newThemeData.Name)
        
        WindUI:Notify({ Title = "Theme Saved", Content = "Theme '" .. newThemeData.Name .. "' has been saved."})
    end
})

-- Inicia o editor com o tema atual
UpdateThemeEditorUI(WindUI:GetCurrentTheme())
