# Assembly_Network-distributor-master

Experimentálny proxy DHCP server napísaný v ARM assembly pre Linux. Projekt bol prenesený z vetvy `master` repozitára [Assembly_Network-distributor](https://github.com/CreperAvatar/Assembly_Network-distributor).

Program počúva na DHCP porte `67` a vytvára DHCP odpoveď pre PXE klientov. Z odpovede poskytuje adresu TFTP servera odvodenú od IP adresy sieťového rozhrania a názov boot súboru `pxelinux.0`. Zdrojový kód nastavuje PXE/DHCP voľby vrátane vendor class `PXEClient`.

> Toto je experimentálny prototyp. Pred použitím v sieti si dôkladne over jeho správanie a konfiguráciu; nesprávne DHCP odpovede môžu ovplyvniť ostatných klientov v sieti.

## Súbory

- `proxyDHCP.s` – zdrojový kód proxy DHCP servera v ARM assembly.
- `proxyDHCP` – zostavený spustiteľný súbor.

## Požiadavky

- 32-bitový ARM Linux kompatibilný so systémovými volaniami v zdrojovom kóde.
- GNU Binutils (`as` a `ld`), ak chceš program zostaviť zo zdrojového kódu.
- Sieťové rozhranie s názvom `eth0`.
- Prístup k UDP portu `67`; spustenie servera bude spravidla vyžadovať oprávnenia root alebo príslušnú capability.
- PXE klient a dostupný TFTP server, ktorý poskytuje boot súbor `pxelinux.0`.

## Zostavenie

V koreňovom adresári projektu spusti:

```sh
as -o proxyDHCP.o proxyDHCP.s
ld -o proxyDHCP proxyDHCP.o
```

## Nastavenie a spustenie

Zdrojový kód má natvrdo nastavené rozhranie `eth0` a názov boot súboru `pxelinux.0`. Uprav tieto hodnoty v `proxyDHCP.s`, ak sa tvoje prostredie líši, a program znovu zostav.

Spustenie:

```sh
sudo ./proxyDHCP
```

Spúšťaj ho iba v testovacej alebo vyhradenej sieti, kde je DHCP/PXE konfigurácia pod tvojou kontrolou. Zabezpeč, aby bol boot súbor dostupný cez TFTP na adrese oznámenej klientovi.

## Bezpečnostné upozornenie

DHCP používa broadcast komunikáciu a server nemá v projekte viditeľnú autentifikáciu. Nesprávne alebo súbežné DHCP služby môžu narušiť prideľovanie sieťových nastavení. TFTP navyše neposkytuje šifrovanie ani autentifikáciu. Používaj tento prototyp len v izolovanej, dôveryhodnej sieti a neposkytuj cez TFTP citlivé súbory.
