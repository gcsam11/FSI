#Trabalho realizado na Semana #3

- Dando Log In no website, fizemos o percurso de Main Page -> Shop -> Wordpress Hosting -> Reviews. Ai vimos que a vulnerabilidade muito provavelmente iria ter a haver com os plugins.
- Voltando para Wordpress Hosting, fomos agora para Additional Information. Aqui vimos que o website tem o plugin WooComerce que se encontra na versão 5.7.1.
- Pesquisando agora em https://www.exploit-db.com/ o plugin, vimos que apenas a vulnerabilidade CVE-2021-34646 se aplicava para a versão que queriamos.
- Agora, corremos em python o script dado em https://www.exploit-db.com/exploits/50299, utilizando como argumentos http://ctf-fsi.fe.up.pt:5001/my-account/ e 1, que corresponde ao id do admin.
- O resultado foi http://ctf-fsi.fe.up.pt:5001/my-account/my-account/?wcj_verify_email=eyJpZCI6IjEiLCJjb2RlIjoiMDQyYWU0MWJlZjM4MDgwZWRjZWNmMjU3MTMxZDI2NTkifQ que deu nos logo acesso à conta do admin. 
- Entramos agora em http://ctf-fsi.fe.up.pt:5001/wp-admin/edit.php, aonde fomos ver a mensagem privada que continha então a flag: flag{please don't bother me}.
