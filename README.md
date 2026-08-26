# ScriptsAD

Comandos Básicos Command:
- Erros de replicação:
		- Use o comando a seguir para ver o menu de ajuda, isso exibirá todas as opções da linha de comando.
			  § repadmin /?
- Veja o status da replicação e visualize a integridade geral
		- Este comando mostrará a porcentagem de tentativas de replicação que falharam, bem como os maiores deltas de replicação.
			  § repadmin / replsummary
- Mostrar parceiro e status de replicação
		- Util para identificar quais objetos estão falhando na replicação.
			  § repadmin / showrepl
- Mostrar parceiro de replicação para um controlador de domínio específico
    - Se você deseja ver o status da replicação para um controlador de domínio específico, use este comando.
			  § repadmin / showrepl <ServerName>
- Mostrar apenas erros de replicação
		-	Use este comando para visualizar a fila de replicação
			  § Repadmin / Queue
- Como forçar a replicação do Active Directory
		-	Isso fará uma replicação pull, o que significa que ele puxará atualizações do DC2 para o DC1.
			  § repadmin / syncall dc1 / AeD
    - Se você fizer alterações no DC1 e desejar replicá-las em outros DCs, use este comando.
			  § repadmin / syncall dc1 / APeD
- Exportar resultados para um arquivo de texto
    -   Basta adicionar> c: \ pasta de destino \ nome de arquivo.txt ao final de qualquer um dos comandos
        § repadmin / replsummary> c: \ it \ replsummary.txt
			  §	repadmin / showrepl> c: \ it \ showrepl.txt
	  - Última vez em que seu controlador de domínio fez um backup
		  	§ Repadmin / showbackup *
- Identificar em qual AD está autenticado:
		    § $env:LOGONSERVER
		 

Comandos Básicos PowerShell:
- Atualizando GPO de computadores remotamente
		○ PsExec \\ Computername Gpupdate
		○ Usando o PowerShell Invoke-GPUpdate:
			§ Invoke-GPUpdate -Computer COMPUTER02 -RandomDelayInMinutes 0
			- O RandomDelayInMinutes 0 especifica o atraso. Definir como 0 atualizará a política de grupo imediatamente.
- Backup remoto do estado do sistema do Active Directory Isso fará o backup dos dados de estado do sistema dos controladores de domínio. Altere o nome do DC para o nome do servidor e altere o caminho do backup. O       caminho do backup pode ser um disco local ou um caminho UNC
		  § invoke-command -ComputerName DC-Name -scriptblock {wbadmin start systemstateback up -backupTarget:"Backup-Path" -quiet} 
- Exibir informações básicas de domínio:
		  § Get-ADDomain 
- Exportar todos os usuários do AD por nome
		  § Get-ADUser -Filter * -Properties * | Select-Object name | export-csv -path c:\export\allusers.scv
- Exportar todos os usuários por nome e último logon
		  § get-aduser –filter * -property * | Select-object Name, LastLogonDate
- Exportar usuários de uma UO específica
		  § Get-ADUser -Filter * -SearchBase "OU=Finance,OU=UserAccounts,DC=FABRIKAM,DC=COM"
- Grupos
		- Listar grupos vazios:
		  § Get-ADGroup -Filter * -Properties Members | where {-not $_.members} | select Name
- Listar 
    - A política de senha do domínio conectado
		  	§ Get-ADDefaultDomainPasswordPolicy
		- Controladores de domínio por nome de host e operação
			  § Get-ADDomainController -filter * | select hostname, operatingsystem
		- Políticas de senha refinadas
			  § Get-ADFineGrainedPasswordPolicy -filter *
		-   Obter propriedades específicas do usuário e da lista
			  - Basta adicionar o que você deseja exibir após selecionar
			      § Get-ADUser username -Properties * | Select name, department, title
		- Tentativas de senha incorreta
			  § get-aduser -filter * -Properties * | select name, badPwdCount, LastBadPasswordAttempt
		- Usuários e todas as propriedades (atributos)
			  § Get-ADUser username -Properties *
		- Usuários de uma UO específica (OU = o caminho distinto da OU)
				§ Get-ADUser -SearchBase “OU=ADPRO Users,dc=ad,dc=activedirectorypro.com” -Filter *
		- Usuários com login iniciado em .... E filtrar propriedades. No final contar quantidade:
				§ (Get-ADUser -filter 'SamAccountName -like "uar*"' -Properties SamAccountName, CN, Modified, Created).count
				    - https://github.com/100security/ad-lista 
 	  - Obter usuários do AD por nome
	    - Este comando encontrará todos os usuários que têm a palavra robert no nome. Basta alterar robert para a palavra que você deseja pesquisar.
		      § get-Aduser -Filter {name -like "*robert*"}
	  - Obter todas as contas de usuário desabilitadas
		      § Search-ADAccount -AccountDisabled | select name
    - Obter todas as contas com senha definida para nunca expirar
		      § get-aduser -filter * -properties Name, PasswordNeverExpires | where {$_.passwordNeverExpires -eq "true" } | Select-Object DistinguishedName,Name,Enabled
  	- Listar todas as contas de usuário desativadas
	    	  § Search-ADAccount -AccountDisabled
		- Apenas o nome dos ativados:
		      § Get-ADUser -Filter 'enabled -eq $true' | select name, GivenName, DistinguishedName, SID | Out-GridView
		- Exportar para CSV:
		      § Get-ADUser -Filter{name -like "*b*" -and enabled -eq $true} | Select-object Samaccountname,givenname,surname,enabled | Export-csv -Path c:\user_enable.csv
  	- Obter todos os membros de um grupo de segurança
		      § Get-ADGroupMember -identity “HR Full”
   	- Obter todos os grupos de segurança
	    - Isso listará todos os grupos de segurança em um domínio
		       § Get-ADGroup -filter *      
- Desativar conta de usuário
		§ Disable-ADAccount -Identity rallen
- Ativar conta de usuário
		§ Enable-ADAccount -Identity rallen
- Localizar todas as contas de usuário bloqueadas
		§ Search-ADAccount -LockedOut
- Desbloquear conta de usuário
		§ Unlock-ADAccount –Identity john.smith 
- Forçar alteração de senha no próximo login
		§ Set-ADUser -Identity username -ChangePasswordAtLogon $true
- Mover um único usuário para uma nova UO
	- Você precisará do distinguishedName do usuário e da UO de destino
		  § Move-ADObject -Identity "CN=Test User (0001),OU=ADPRO Users,DC=ad,DC=activedirectorypro,DC=com" -TargetPath "OU=HR,OU=ADPRO Users,DC=ad,DC=activedirectorypro,DC=com"
- Mover usuários para uma UO de um CSV
	- Configure um csv com um campo de nome e uma lista dos usuários sAmAccountNames. Em seguida, basta alterar o caminho da UO de destino.
		§ Specify target OU. $TargetOU = "OU=HR,OU=ADPRO Users,DC=ad,DC=activedirectorypro,DC=com" # Read user sAMAccountNames from csv file (field labeled "Name"). Import-Csv -Path Users.csv | ForEach-Object { # Retrieve DN of User. $UserDN = (Get-ADUser -Identity $_.Name).distinguishedName # Move user to target OU. Move-ADObject -Identity $UserDN -TargetPath $TargetOU }
	- Adicionar usuário ao grupo
	- Altere o nome do grupo para o grupo do AD ao qual você deseja adicionar usuários
		○ Add-ADGroupMember -Identity group-name -Members Sser1, user2
 
	- Exportar usuários de um grupo
	- Isso exportará os membros do grupo para um CSV, alterará o nome do grupo para o grupo que você deseja exportar.
		○ Get-ADGroupMember -identity “Group-name” | select name | Export-csv -path C:OutputGroupmembers.csv -NoTypeInformation
 
	- Obter grupo por palavra-chave
	- Encontre um grupo por palavra-chave. Útil se você não tiver certeza do nome, altere o nome do grupo.
		○ get-adgroup -filter * | Where-Object {$_.name -like "*group-name*"}
 
	- Importar uma lista de usuários para um grupo
		○ $members = Import-CSV c:itadd-to-group.csv | Select-Object -ExpandProperty samaccountname Add-ADGroupMember -Identity hr-n-drive-rw -Members $members
 
	- Comandos do computador AD
	- Obter todos os computadores
	- Isso listará todos os computadores no domínio
		○ Get-AdComputer -filter *
 
	- Obter todos os computadores por nome
	- Isso listará todos os computadores no domínio e exibirá apenas o nome do host
		○ Get-ADComputer -filter * | select name
 
	- Obter todos os computadores de uma UO
		○ Get-ADComputer -SearchBase "OU=DN" -Filter *
 
	- Obter uma contagem de todos os computadores no domínio
		○ Get-ADComputer -filter * | measure
 
	- Obtenha todos os computadores com Windows 10
	- Altere o Windows 10 para qualquer sistema operacional que você deseja pesquisar. Este é um dos principais comandos PowerShell para Active Directory
		○ Get-ADComputer -filter {OperatingSystem -Like '*Windows 10*'} -property * | select name, operatingsystem
 
	- Obter uma contagem de todos os computadores por sistema operacional
	- Isso fornecerá uma contagem de todos os computadores e os agrupará pelo sistema operacional. Um ótimo comando para fornecer um inventário rápido de computadores no AD.
		○ Get-ADComputer -Filter "name -like '*'" -Properties operatingSystem | group -Property operatingSystem | Select Name,Count
 
	- Excluir um único computador
		○ Remove-ADComputer -Identity "USER04-SRV4"
 
	- Excluir uma lista de contas de computador
	- Adicione os nomes de host a um arquivo de texto e execute o comando abaixo.
		○ Get-Content -Path C:ComputerList.txt | Remove-ADComputer
 
	- Excluir computadores de uma UO
		○ Get-ADComputer -SearchBase "OU=DN" -Filter * | Remote-ADComputer
 
	- Seção Política de Grupo
	- Obter todos os comandos relacionados ao GPO
		○ get-command -Module grouppolicy
 
	- Obter todos os GPOs por status
		○ get-GPO -all | select DisplayName, gpostatus
 
	- Faça backup de todos os GPOs no domínio
		○ Backup-Gpo -All -Path E:GPObackup
	- Habilitar Lixeira do Windows
		○ Import-module ActiveDirectory
		○ Execute o seguinte cmdlet para habilitar a Lixeira
			§ Enable-ADOptionalFeature 'Recycle Bin Feature' -Scope ForestOrConfigurationSet -Target <your forest root domain name>
		○ Aqui está um exemplo usando o domínio ad.uniacademy.com.br
			§ Enable-ADOptionalFeature 'Recycle Bin Feature' -Scope ForestOrConfigurationSet -Target ad.uniacademy.com.br
		○ Como verificar se a lixeira do AD está habilitada
			§ Get-ADOptionalFeature -filter *

	- Usuário
		○ Listar usuários bloqueados:
			§ Search-ADAccount -lockedout | Select-Object Name, SamAccountName

