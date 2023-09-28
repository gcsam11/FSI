# Trabalho realizado nas Semanas #2 e #3

## Identificação

- Family of malicious Java applets that attempt to exploit a vulnerability (CVE-2012-4681) in the Java Runtime Environment (JRE), in order to download and install files of an attacker’s choice onto your computer.
- If you visit a website containing the malicious code while using a vulnerable version of Java, Exploit:Java/CVE-2012-4681 is loaded. It then attempts to download and execute files from a remote host/URL; the files that are downloaded and executed could include additional malware.
- This vulnerability exists in multiple platforms, including: IE, Firefox, Safari, Chrome; Windows, Ubuntu, OS X, Solaris, etc.

## Catalogação

- NVD Published Date: 27/08/2012
- NVD Last Modified: 21/12/2022
- According to [cvedetails.com], the severity score is 10.0/10.0 and so is the exploitability score.

## Exploit

- The exploit takes advantage of two issues in JDK 7: The ClassFinder and MethodFinder.findMethod(). It allows untrusted code to obtain a reference and have access to a restricted package in JDK 7, which can be used to abuse sun.awt.SunToolkit. 
- With sun.awt.SunToolkit, we can actually invoke getField() by abusing findMethod() in Statement.invokeInternal() in order to access Statement.acc's private field, modify AccessControlContext, and then disable Security Manager. 
- Once Security Manager is disabled, we can execute arbitrary Java code. 
- Oracle Java JDK and JRE 7 Update 5 and earlier, JRE 6 Update 34 and earlier are all vulnerable to this exploit.

## Ataques

- "According to media reports, attacks so far have only been targeted and not widespread." (https://www.gdatasoftware.com/blog/2012/08/24036-cve-2012-4681-a-java-0-day-is-going-to-hit-big-time)
