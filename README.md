# Protokoll 3 <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/30/HTL_Kaindorf_Logo.svg/300px-HTL_Kaindorf_Logo.svg.png" alt="">  
  
Professor: SX  
Übungsort: Kaindorf Aut-Labor  
Übungsdatum: 02.10.2018  
Anwesend: Vollmaier Alois, Vezonik Sarah, Wegl Patrick, Wesonig Mercedes, Winter Matthias, Winter Thomas  
  
### Atmel Studio  
Das Atmel Studio ist eine kostenlose Entwicklungsumgebung (IDE) für die Programmierung der AVR-Mikrocontroller und ARM-Mikrocontroller von Atmel. Sie basiert ab Version 5 auf der Visual Studio Shell von Microsoft und besteht aus einer Projektverwaltung, einem Editor, einem Debugger und Werkzeugen zum Beschreiben der Mikrocontroller.  
Mit dem Atmel Studio kann in Assembler sowie in C/C++ programmiert werden. Für die Unterstützung von C/C++ musste bis einschließlich Version 4 vor der Installation des AVR Studio der GNU C Compiler für AVRs WinAVR installiert werden. Ab AVR Studio 5 ist eine vollständige Toolchain zur Entwicklung von C-Projekten enthalten.
<img src="http://micrium.com/wp-content/uploads/2012/08/Atmel-Logo.png">  

#### Simulation des Übungsbeispiels  
Um das Prinzip der AVR-Software zu verstehen wurde ein einfaches Programm geschrieben, welches 0 als Rückgabewert zurück gibt.  
Das Programm aus der Übung sieht wie folgt aus:  
``` #include <avr/io.h>  
int main (void)  
{  
  return 0;  
}  
```  
#### Disassembliertes Programm
``` 
0000003D  LDI R24,0x00		Load immediate
0000003E  LDI R25,0x00		Load immediate
0000003F  RET 		Subroutine return   
```  
Die beiden Register mit dem Namen **R24** und **R25** werden für diesen Rückgabewert verwendet. Mit LDI´R24, R25 wird in beide Register eine 0 geschrieben, somit ist der Rückgabewert des Programms 0.

Da nun die Funktionsweise der AVR-Software verstanden wurde, wurde ein Zweites Programm geschrieben welches eine LED am PORTB ansteuert.

``` 
#include <avr/io.h>

int main()
{  
  DDBR =0x20;  
  PORTB =0x20;  
  return 0;  
}  
```  
##### Startup Routine:
  * R1 wird auf 0 gesetzt
  * Die Statusflags werden zurückgesetzt
  * Der Stack-Pointer wird initialisiert
  * Ein Behl "Spring zum eigentlichen Programm" wird ausgeführt  
  
#### Übersicht der wichtigsten Assemblerbefehle aus der Laboreinheit  
**1. Unbedingter Sprung** ``` Jump JMP adr ```  

**2. Ein-/Ausgabebefehle**
  Lade A vom Port n    ``` Input    IN A,n ```  
  Gib A aus auf Port n ``` Output   Out A,n```  
  
**3. Transportbefehle**  
  Lade Register A aus n       ``` Load      LDA m```  
  Speichere Register A in n   ``` Store     STA m```  
  Transportiere n2 nach n1    ``` Move      MOV n1,n2```  
  Lade 0 in Register A        ``` Clear     CLRA```  
    
**4. Arithmetische Befehle**  
  Addiere Speicherinhalt zu A ```Add    ADDA n```  
  Subtrahiere Speicherinhalt von A  ```Subtract     SUBA n```  
  Multipliziere A mit n   ```Multiply     MULA n```  
  Erhöhe A um 1     ```Increment      INCA```  
  Vermindere A um 1 ```Decrement      DECA```  
    
### Grundlagen einer CPU  
<img src="https://img.picload.org/image/dcllprii/cpu_svg.png" alt="enter image description here"><br>
Quelle:  <a href="https://lms.at/dotlrn/classes/digi/610437.4AHME_FIV.18_19/xolrn/EC743ABCF7AB5.symlink?resource_id=0-300238136&amp;m=view#188315330">FIV-Skriptum </a></li>  

* **Die Register**  
Das sind Speichereinheiten die direkt mit der Recheneinheit verbunden sind. Sie beinhalten Daten, Operanden und          Berechnungsergebnisse in Form von Dualzahlen, und haben in der Regel die „Wortgröße“ des Prozessors (8 Bit, 16 Bit, 32 Bit oder 64 Bit). Schaltungstechnisch handelt es sich hier meist um taktflankengesteuerte D-Flip-Flops.
  
* **Das Rechenwerk**  
(die „Arithmetic Logic Unit“ oder kurz ALU)
Hier handelt es sich um ein digitales Schaltwerk zur Durchführung arithmetisch/logischer Befehle (addieren, subtrahieren, schieben, rotieren, vergleichen, NOT, AND, OR, XOR).  
  
* **Das Steuerwerk**
Das Steuerwerk ist für die Abarbeitung des Programms verantwortlich. Es arbeitet das Programm (also die Befehlskette) Befehl für Befehl ab. Im Steuerwerk befindet sich der Befehlszähler (Programcounter, Instructionpointer) und der Befehls-Decoder.  
  
* **Die Speicherverwaltungseinheit**  
Diese ist für den Zugriff auf den Arbeitsspeicher verantwortlich. Bei großen Prozessoren ist sie durch eine Memory Manangement Unit (MMU) realisiert. Bei der MMU werden gleiche virtuelle Adressen von unterschiedlichen Prozessen auf unterschiedliche physikalische Adressen im realen Arbeitsspeicher abgebildet, damit sich unterschiedliche Prozesse (Programme) nicht gegenseitig beeinflussen können.  
  
  
### Speicherarten
Grundsätzlich wird aus sicht des Programmierers zwischen Arbeitsspeicher und Programmspeicher unterschieden.  
Im Programmspeicher ist das eigentliche Programm, also die Maschinenbefehle, zu finden. Der Arbeitsspeicher wird während der Ausführung des Programmes (während der Arbeit) für Variable, Rücksprungadressen, ... benötigt.  
Ob für diese beiden Speicherarten tatsächlich verschiedene Speicherbausteine (bzw. Technologien) verwendet werden, oder ob diese sich in einem gemeinsamen (physikalischen) Speicher befinden, ist systemabhängig.  
In einem PC-System (zB x86- oder amd64-Architektur) sind Programm- und Arbeitstspeicher gemeinsam im DRAM (siehe unten) zu finden.  
Auf einem Atmel 8-Bit µC System ist der Arbeitsspeicher in einem statischen RAM-Modul (SRAM) zu finden, während die Maschinenbefehle in einem FLash abgelegt sind. Beide Speicher befinden sich im µC Baustein.  
Die Zugriffsgeschwindigkeit auf den Speicher hat maßgeblichen Einfluss auf die Arbeitsgeschwindigkeit des Systems. Daher sind bei schnellen Systemen im Prozessor zusätzlich schnelle Speicher eingebaut, die Daten und Befehle temporär speichern, sogenannte Cache-Speicher.  
Die wichtigsten Merkmale eines Speicherbausteins sind:  
  * Typ und Technologie
  * Speichergröße
  * Zugriffsgeschwindigkeit
  * Preis

#### Speicherarten und Technologien

**RAM** (Random Access Memory)  
Mit Hilfe einer Adresse kann auf den Speicherinhalt lesend oder schreibend zugegriffen werden.  
**ROM** (Read Only Memory)  
Mit Hilfe einer Adresse wird auf den Speicherinhalt lesend zugegriffen. Der Dateninhalt ist (durch die Herstellung)  vorgegeben.  
**PROM** (Programmable ROM):  
Programmierbares ROM.  
**OTP-ROM** (One Time Programable ROM):  
Nur einmal programmierbares ROM  
**EPROM** (Erasable PROM):  
Wiederprogrammierbares ROM. Kann mit UV-Licht gelöscht werden.  
**EEPROM** (Electrical Eraseable PROM):  
Wiederprogrammierbares ROM. Kann auf elektrische Weise gelöscht werden, zB durch eine höhere Spannung an einem speziellen Pin.  
**Flash**  
Eine besondere Bauform eines EEPROM. Das Löschen und Reprogrammieren erfolgt hier besonders schnell (blitzschnell, Flash = Blitz).  
