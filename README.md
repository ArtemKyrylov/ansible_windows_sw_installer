# ansible_windows_sw_installer
WinRM usage with ansible

"WinRM allows run shell commands remotely"

1. Enable and Configure WinRM (Windows Remote Manager)
   Create Local windows user with name for ex.: "Ansible"
   Add Ansible user to Administrator group (it is allow install software)
   
2. Verify Power Shell version and .NET lib. version
   Run Power Shell cmd with administrator rights
   Run command in power shell for "Power Shell" version verification: "Get-Host | Select-Object Version"
   Run command in power shell for ".NET" version verification: Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP' -Recurse | 
   Get-ItemProperty -Name version -EA 0 | Where { $_.PSChildName -Match '^(?!S)\p{L}'} | Select PSChildName, version
   
3. Verify That WinRM was not configured before
   Run Power Shell cmd with administrator rights
   Run command in power shell: winrm get winrm/config/Service, command must be failed with "WSManFault" error
   Run command in power shell: winrm get winrm/config/Winrs, command must be failed with "WSManFault" error
   Run command in power shell: winrm enumerate winrm/config/Listener, command must be failed with "WSManFault" error
      
4. Setup WinRM
   Run Power Shell cmd with administrator rights
   Run command in power shell: [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   Run command in power shell: $url = "https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1"
   Run command in power shell: $file = "$env:temp\ConfigureRemotingForAnsible.ps1"
   Run command in power shell: (New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)
   Run command in power shell: powershell.exe -ExecutionPolicy ByPass -File $file
   
   OR
   Run Power Shell cmd with administrator rights
   Run command in power shell:  Enable-PSRemoting OR winrm quickconfig
   Enable WinRM basic authentication manually by command: winrm set winrm/config/service/auth '@{Basic="true"}'
   
5. Verify WinRM configuration
   Run Power Shell cmd with administrator rights
   Run command in power shell: winrm get winrm/config/Service, command must show authentication rules and win rm rules, for ex.: Basic = true, HTTP = 5985, 
   HTTPS = 5986
   Run command in power shell: winrm get winrm/config/Winrs, command must show:  AllowRemoteShellAccess = true
   Run command in power shell: winrm enumerate winrm/config/Listener, check winrm PORT, Enabled = true, ListeningOn.
   
6. Test connection on ansible controller with windows remote server
   Create host file with Windows server entry
   Get ansible playbook: ansible_playbook_windows_server_connection_verify
   
7. Add trusted hosts if needed
   Run command in power shell:  Set-Item WSMan:\localhost\Client\TrustedHosts -Value '*'
   
   
   
