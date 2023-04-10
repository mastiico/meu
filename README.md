
@echo off
setlocal enabledelayedexpansion

color 6

::Imprime o logo do programa
echo $$^\      $$\  $$$$$$\   $$$$$$\ $$$$$$$$\ $$$$$$\  $$$$$$\   $$$$$$\
echo $$$^\    $$$ ^|$$  __$$\ $$  __$$\\__$$  __^|\_$$  _^|$$  __$$\ $$  __$$\ 
echo $$$$^\  $$$$ ^|$$ /  $$ ^|$$ /  \__^|  $$ ^|     $$ ^|  $$ /  \__^|$$ /  $$ ^|
echo $$^\$$^\$$ $$ ^|$$$$$$$$ ^|\$$$$$$\    $$ ^|     $$ ^|  $$ ^|      $$ ^|  $$ ^|
echo $$ ^\$$$  $$ ^|$$  __$$ ^| \____$$\   $$ ^|     $$ ^|  $$ ^|      $$ ^|  $$ ^|
echo $$ ^|^\$  ^/$$ ^|$$ ^|  $$ ^|$$\   $$ ^|  $$ ^|     $$ ^|  $$ ^|  $$\ $$ ^|  $$ ^|
echo $$ ^| ^\_^/ $$ ^|$$ ^|  $$ ^|\$$$$$$  ^|  $$ ^|   $$$$$$\ \$$$$$$  ^| $$$$$$  ^|
echo ^\__^|     ^\__^|\__^|  \__^| \______/   \__^|   \______^| \______/  \______/ 



:: Validação de entrada
echo.
echo Informacoes do Cliente:
echo ----------------------
set /p "cpf=CPF Cliente (11 digitos): "
set /p "nome=Nome Cliente: "

:: Adicionar produtos
set /p "add_produto=Adicionar produtos? (Sim/Nao): "
set "produtos="
set "valor_produtos=0"
set "produtos_com_valor="
:loop
if /i "%add_produto%"=="sim" (
    set /p "produto=Nome do produto: "
    set /p "valor=Valor do produto: "
    set "produtos=!produtos!- !produto!"
    set /a "valor_produtos=!valor_produtos!+!valor!"
    set "produtos_com_valor=!produtos_com_valor! !produto! no valor de: R$!valor!^

"
    set /p "add_produto=Adicionar mais produtos? (Sim/Nao): "
    goto loop
)

:: Continua validação de entrada
set /p "juros=Juros: "
set /p "valor_total=Valor total: "
set /p "parcelas=Quantidade de parcelas: "
set /p "reducao=Reducao sofrida: "
set /p "protocolo=Protocolo: "

:: Formatação de saída
echo.
echo Informacoes do cliente:
echo ----------------------
echo CPF:         %cpf%
echo Nome:        %nome%
echo Produtos:    %produtos%
echo.
echo Produtos com seus valores:
echo ------------------------
echo %produtos_com_valor%
echo.
echo Valor dos produtos: %valor_produtos%
echo Juros:       %juros%
echo Valor total: %valor_total%
echo Parcelas:    %parcelas%
echo Reducao:     %reducao%%
echo Protocolo:   %protocolo%
echo.

:: Define a pasta onde serão salvos os resultados
set "folder=%~dp0\resultados"

:: Verifica se a pasta existe, senão cria
if not exist "%folder%" (
  mkdir "%folder%" || (
    echo Não foi possível criar a pasta de resultados.
    pause
    goto :eof
  )
)

:: Conta o número de arquivos na pasta para definir o número do arquivo atual
set count=0
for /f %%f in ('dir /b /a-d "%folder%\*.*" 2^>nul') do set /a "count+=1"

:: Define o número do arquivo atual e a data/hora atual
for /f "tokens=1-3 delims=/ " %%a in ("%date%") do (
  set "datestamp=%%a-%%b-%%c"
)
set "timestamp=%time:~0,2%-%time:~3,2%-%time:~6,2%"

:: Define o nome do arquivo a ser criado
set "filename=%folder%\#%count%-%nome%-%timestamp%.txt"

:: Salva informações no arquivo
(
  echo Cpf:         "%cpf%"
  echo Nome:        "%nome%"
  echo Produtos com seus valores:
  echo ------------------------
  echo %produtos_com_valor%
  echo Valor dos produtos: %valor_produtos%
  echo Juros:       "%juros%"
  echo Valor total: "%valor_total%"
  echo Parcelas:    "%parcelas%"
  echo Redução:     "%reducao%"
 

  echo Protocolo:   "%protocolo%"
) > "%filename%"

echo As informações foram salvas com sucesso no arquivo %filename%.

:: Atualiza o contador de arquivos na pasta
echo %count% > "%folder%\count.txt"

pause
