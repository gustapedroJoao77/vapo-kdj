local OrionLib =  loadstring ( jogo : HttpGet ( ( 'https://raw.githubusercontent.com/shlexware/Orion/main/source' ) ) ) ( )
Janela local = OrionLib : MakeWindow ( { Nome =  "Universal Aimlock GUI v2" , HidePremium =  false , SaveConfig =  true , ConfigFolder =  "OrionTest" } )
 
-- Aba principal
Tab local = Janela : CriarTab ( {
    Nome =  "Aimbot" ,
    Ícone =  "rbxassetid://4483345998" ,
    PremiumOnly =  falso
} )
 
-- Seções
AimbotSection local = Guia : AdicionarSeção ( {
    Nome =  "Configurações do Aimbot"
} )
 
local TargetSection = Guia : AdicionarSeção ( {
    Nome =  "Configurações de destino"
} )
 
ESPSection local = Guia : AdicionarSeção ( {
    Nome =  "Configurações ESP"
} )
 
-- Variáveis
aimbot localHabilitado =  falso
local aimAtPart =  "HumanoidRootPart"
wallCheckEnabled local =  falso
NPCs alvo locais =  falso
equipe localCheckEnabled =  falso
local headSizeEnabled =  falso
espEnabled local =  falso
 
-- Funções
 função local getClosestTarget ( )
    Cam local = área de trabalho . CâmeraAtual
    Jogadores locais = jogo : GetService ( "Jogadores" )
    local localPlayer = Jogadores . LocalPlayer
    caractere local = localPlayer . Caractere ou localPlayer . PersonagemAdicionado : Esperar ( )
    local localRoot = caractere : WaitForChild ( "HumanoidRootPart" )
    local mais próximoTarget =  nulo
    distância mais curta local =  matemática . enorme
 
     função local checkTarget ( alvo )
        se alvo e alvo : IsA ( "Modelo" )  e alvo : FindFirstChild ( "Humanóide" )  e alvo : FindFirstChild ( aimAtPart )  então
            local targetRoot = alvo [ aimAtPart ]
            distância local =  ( targetRoot.Position - localRoot.Position ) . Magnitude​​​​
 
            se distância < shortestDistance então
                se wallCheckEnabled então
                    local rayDirection =  ( targetRoot . Position - Cam . CFrame . Position ) . Unidade *  1000
                    raycastParams local = RaycastParams . novo ( )
                    raycastParams . FilterDescendantsInstances =  { caractere }
                    raycastParams . FilterType = Enum . RaycastFilterType . Lista negra
 
                    local raycastResult = espaço de trabalho : Raycast ( Cam . CFrame . Position , rayDirection , raycastParams )
 
                    se raycastResult e raycastResult . Instância : IsDescendantOf ( alvo )  então
                        shortestDistance = distância
                        nearestTarget = alvo
                    fim
                outro
                    shortestDistance = distância
                    nearestTarget = alvo
                fim
            fim
        fim
    fim
 
    para _ , jogador em  pares ( Jogadores : GetPlayers ( ) )  faça
        se player ~= localPlayer e  ( não teamCheckEnabled ou player . Team ~= localPlayer . Team )  então
            checkTarget ( jogador . Personagem )
        fim
    fim
 
    se alvos NPCs então
        para _ , npc em  pares ( workspace : GetDescendants ( ) )  faça
            verificarAlvo ( npc )
        fim
    fim
 
    retornar mais próximoTarget
fim
 
 função local lookAt ( targetPosition )
    Cam local = área de trabalho . CâmeraAtual
    se targetPosition então
        Cam . CFrame = CFrame . novo ( Cam . CFrame . Posição , targetPosition )
    fim
fim
 
 função local aimAtTarget ( )
    local runService = jogo : GetService ( “RunService” )
    conexão local
    conexão = runService . RenderStepped : Conectar ( função ( )
        se  não aimbotEnabled então
            conexão : Desconectar ( )
            retornar
        fim
 
        localmaispróximoTarget = getClosestTarget ( )​
        se closerTarget e closerTarget : FindFirstChild ( aimAtPart )  então
            local targetRoot = alvomaispróximo [ aimAtPart ]
 
            enquanto aimbotEnabled e closerTarget e closerTarget : FindFirstChild ( aimAtPart )  e closerTarget . Humanóide . Saúde >  0  faça
                lookAt ( targetRoot . Posição )
                local rayDirection =  ( targetRoot . Position - workspace . CurrentCamera . CFrame . Position ) . Unidade *  1000
                raycastParams local = RaycastParams . novo ( )
                raycastParams . FilterDescendantsInstances =  { caractere }
                raycastParams . FilterType = Enum . RaycastFilterType . Lista negra
 
                local raycastResult = espaço de trabalho : Raycast ( espaço de trabalho . CurrentCamera . CFrame . Position , rayDirection , raycastParams )
 
                se  não raycastResult ou  não raycastResult . Instância : IsDescendantOf ( mais próximoTarget )  então
                    quebrar
                fim
 
                runService . RenderStepped : Aguarde ( )
            fim
        fim
    fim )
fim
 
 função local resizeHeads ( )
    Jogadores locais = jogo : GetService ( "Jogadores" )
    local localPlayer = Jogadores . LocalPlayer
 
     função local resizeHead ( modelo )
        cabeça local = modelo : FindFirstChild ( "Cabeça" )
        se cabeça e cabeça : IsA ( "BasePart" )  então
            cabeça . Tamanho = Vector3 . novo ( 5 ,  5 ,  5 )
            cabeça . CanCollide =  falso
        fim
    fim
 
    para _ , jogador em  pares ( Jogadores : GetPlayers ( ) )  faça
        se jogador ~= localPlayer e jogador . Personagem então
            resizeHead ( jogador . Personagem )
        fim
    fim
 
    para _ , npc em  pares ( workspace : GetDescendants ( ) )  faça
        se npc : IsA ( "Modelo" )  e npc : FindFirstChild ( "Cabeçalho" )  então
            redimensionarCabeça ( npc )
        fim
    fim
fim
 
 função local createESP ( )
    para _ , jogador em  pares ( jogo : GetService ( "Jogadores" ) : GetPlayers ( ) )  faça
        se jogador ~= jogo . Jogadores . LocalPlayer e jogador . Personagem e jogador . Personagem : FindFirstChild ( "Cabeça" )  então
            local head = jogador . Personagem . Cabeça
            outdoor local = Instância . new ( "BillboardGui" )
            outdoor . Adornee = cabeça
            outdoor . Tamanho = UDim2 . novo ( 0 ,  100 ,  0 ,  50 )
            outdoor . StudsOffset = Vector3 . novo ( 0 ,  2 ,  0 )
            outdoor . AlwaysOnTop =  verdadeiro
 
            textLabel local = Instância . novo ( "TextLabel" )
            textLabel . Pai = outdoor
            textLabel . Tamanho = UDim2 . novo ( 1 ,  0 ,  1 ,  0 )
            textLabel . Texto = jogador . Nome
            textLabel . TransparênciaDeFundo =  1
            textLabel .TextStrokeTransparência = 0​ 
            textLabel . TextScaled =  verdadeiro
 
            se jogador . Equipe então
                textLabel . TextColor3 = jogador . Equipe . Cor da Equipe . Cor
            outro
                textLabel . CorDoTexto3 = Cor3 . novo ( 1 ,  1 ,  1 )
            fim
 
            outdoor . Pai = cabeça
        fim
    fim
fim
 
 função local removeESP ( )
    para _ , jogador em  pares ( jogo : GetService ( "Jogadores" ) : GetPlayers ( ) )  faça
        se jogador . Personagem e jogador . Personagem : FindFirstChild ( "Cabeça" )  então
            para _ , criança em  pares ( jogador . Personagem . Cabeça : GetChildren ( ) )  faça
                se criança : IsA ( "BillboardGui" )  então
                    criança : Destruir ( )
                fim
            fim
        fim
    fim
fim
 
-- Elementos GUI
Guia : AdicionarAlternar ( {
    Nome =  "Habilitar Aimbot" ,
    Padrão =  falso ,
    Retorno de chamada =  função ( Valor )
        aimbotEnabled = Valor
        se aimbotEnabled então
            ApontarParaAlvo ( )
        fim
    fim
} )
 
Guia : AdicionarBotão ( {
    Nome =  "Trocar parte de mira" ,
    Retorno de chamada =  função ( )
        se aimAtPart ==  "HumanoidRootPart"  então
            aimAtPart =  "Cabeça"
        outro
            aimAtPart =  "ParteRaizHumanoide"
        fim
        OrionLib : MakeNotification ( {
            Nome =  "Parte do Objetivo" ,
            Conteúdo =  "Agora visando: "  .. aimAtPart ,
            Imagem =  "rbxassetid://4483345998" ,
            Tempo =  5
        } )
    fim
} )
 
Guia : AdicionarAlternar ( {
    Nome =  "Habilitar Verificação de Parede" ,
    Padrão =  falso ,
    Retorno de chamada =  função ( Valor )
        wallCheckEnabled = Valor
    fim
} )
 
Guia : AdicionarAlternar ( {
    Nome =  "NPCs alvo" ,
    Padrão =  falso ,
    Retorno de chamada =  função ( Valor )
        targetNPCs = Valor
    fim
} )
 
Guia : AdicionarAlternar ( {
    Nome =  "Habilitar verificação de equipe" ,
    Padrão =  falso ,
    Retorno de chamada =  função ( Valor )
        teamCheckEnabled = Valor
    fim
} )
 
Guia : AdicionarAlternar ( {
    Nome =  "Redimensionar Cabeças" ,
    Padrão =  falso ,
    Retorno de chamada =  função ( Valor )
        headSizeEnabled = Valor
        se headSizeEnabled então
            redimensionarCabeças ( )
        fim
    fim
} )
 
Guia : AdicionarAlternar ( {
    Nome =  "Etiquetas ESP" ,
    Padrão =  falso ,
    Retorno de chamada =  função ( Valor )
        espEnabled = Valor
        se espEnabled então
            criarESP ( )
        outro
            removerESP ( )
        fim
    fim
} )
 
OrionLib : Inicialização ( )
