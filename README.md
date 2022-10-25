# Grundwasser Daten

Das Grundwasser in Deutschland sinkt dramatisch. Erstmals hat CORRECTIV Daten von rund 6.700 Grundwassermessstellen ausgewertet.

Die Analyse liefert erschreckende Befunde: An knapp der Hälfte aller ausgewerteten Messstellen ist das Grundwasser in den Dürrejahren zwischen 2018 und 2021 auf den tiefsten Stand seit 1990 gefallen. Insgesamt ist in den vergangenen 32 Jahren der Grundwasserstand mehr gesunken als gestiegen.

Lesen Sie hier unsere Recherche: [Wo in Deutschland das Grundwasser sinkt](tk_link)

## Methodik

Grundlage unserer Analyse sind Rohdaten von Grundwassermessstellen aus 13 Bundesländern. Wir haben die Daten durch eine Kombination aus Scraping, also dem Runterladen von Daten von den Webseiten der Behörden, und Presseanfragen gesammelt. Die von uns ausgewerteten Grundwassermessstellen liegen in unterschiedlichen Tiefen.

In einigen Bundesländern werden die Grundwasserstände täglich gemessen. In anderen wird jede zweite Woche oder einmal im Monat gemessen. Um die Daten zu normalisieren, haben wir im nächsten Schritt für jede Messstelle den durchschnittlichen Grundwasserstand für jeden Monat zwischen 1990 und 2021 berechnet. Wenn für eine Messstelle weniger als 95 Prozent der Monatsdaten zur Verfügung standen, haben wir sie aus unserer Analyse ausgeschlossen. In Bremen, Hamburg und Saarland war nach diesem Kriterium die Datenlage nicht ausreichend für unsere Auswertung.

Zusätzlich führten wir einen halbautomatischen Test auf Datengenauigkeit durch. Dafür haben wir das [Changepoint package](https://rdrr.io/cran/changepoint/man/cpt.mean.html) in R verwendet. Wir untersuchten für jede Messstelle sprunghafte Veränderungen im Werteverlauf, was auf Fehler in den Daten hindeutet. Wenn beispielsweise eine Messstelle ausgetauscht wird und die Messungen nicht für die neue Höhe neu kalibriert werden, kann es den Anschein haben, dass der Grundwasserspiegel plötzlich gestiegen ist. Anschließend haben wir die Daten für jede Messstelle, bei der solche Sprünge identifiziert wurden, visuell überprüft und die fehlerhaften Stationen aus unserer Analyse entfernt.

Schließlich berechneten wir den 32-Jahres-Trend mithilfe des Mann-Kendall-Trendtests, der auf langfristig steigende oder fallende Trends prüft. Wir haben speziell die [Trend-Free Pre-Whitening-Variante](https://rdrr.io/cran/modifiedmk/man/tfpwmk.html) des Tests verwendet. Die Analyse ergab den Trend als Veränderung in Metern pro Monat. Um die Daten zu normalisieren, teilten wir dann jeden Trend durch den Werteumfang (Differenz zwischen dem höchsten und dem niedrigsten Monatsmittelwert) für jede Messstelle und rechneten ihn auf ein Jahr hoch, um den Trend in Prozent pro Jahr ausdrücken zu können.

Die Trends werden wie folgt klassifiziert:

Trend                                         | Klassifizierung    |
--------------------------------------------- | ------------------ |
niedriger als -1 Prozent pro Jahr             | stark sinkend      |
-1 bis -0,5 Prozent pro Jahr                  | leicht sinkend     |
-0,5 bis 0,5 Prozent pro Jahr (oder p > 0.05) | kein starker Trend |
0,5 bis 1 Prozent pro Jahr                    | leicht steigend    |
stärker als 1 Prozent pro Jahr                | stark steigend     |

Wir haben [diese Auswertung](https://www.google.com/url?q=https://www.umwelt.niedersachsen.de/startseite/themen/wasser/grundwasser/grundwasserbericht_niedersachsen/grundwassermenge/einfuhrung/trendanalyse/auswertungen-105741.html&sa=D&source=docs&ust=1666630468290243&usg=AOvVaw3XjlZDnmTe6a3QWsaA7UcD) des Niedersächsischen Ministeriums für Umwelt, Energie, Bauen und Klimaschutz als Orientierung genutzt.

Eine Beispielanalyse für ausgewählte Messstationen finden Sie in [beispiel_trendanalyse.ipynb](beispiel_trendanalyse.ipynb).

Wir haben auch berechnet, in welchen Jahren jede Messstelle ihren Tiefststand erreicht hat, und die Daten nach Kreisen zusammengefasst. Ein Beispiel für die Berechnung finden Sie unter [beispiel_tiefststand.ipynb](beispiel_tiefststand.ipynb).

## Die Daten

Monatliche Daten der Grundwassermessstellen nach Bundesländern finden Sie als CSV-Datei im [monthly](monthly/). Jede Datei enthält die folgenden Daten:

- ms_nr
- year
- month
- min_gwl (in Metern)
- mean_gwl (in Metern)
- max_gwl (in Metern)

Stammdaten und die in unserer Analyse berechneten Trends sind in der Datei [trends.csv](trends.csv) enthalten. Hier eine Übersicht über alle Variablen:

- bundesland (Bundesland, in dem sich die Messstelle liegt)
- behoerde (Behörde, die die Messstelle betreibt)
- kreis
- BEZ
- AGS
- ms_nr
- latitude
- longitude
- pct_data_1990_2021 (Prozentsatz der verfügbaren Monatsmittelwerte, 1990-2021)
- trend_normalized (Prozent pro Jahr)
- trend_bin (Ausprägung, von stark sinkend bis stark steigend)
- trend_raw (Meter pro Jahr)
- range (Spanne der Monatsmittelwerte in Metern)
- p_value

## Datenquellen

| Bundesland             | Quelle                                                                                                                                                            |
|------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Baden-Württemberg      | [Landesanstalt für Umwelt Baden-Württemberg](https://udo.lubw.baden-wuerttemberg.de/public/)                                                                      |
| Bayern                 | [Bayerisches Landesamt für Umwelt](https://www.gkd.bayern.de/de/grundwasser/oberesstockwerk)                                                                      |
| Berlin                 | [Berlin Senatsverwaltung für Umwelt, Mobilität, Verbraucher- und Klimaschutz](https://wasserportal.berlin.de/messwerte.php?anzeige=tabelle&thema=gws&nstoffid=10) |
| Brandenburg            | [Ministerium für Landwirtschaft, Umwelt und Klimaschutz (MLUK)](https://apw.brandenburg.de/)                                                                      |
| Bremen                 | [Senat für Umwelt](https://www.bauumwelt.bremen.de/)                                                                                                              |
| Hamburg                | [Behörde für Umwelt, Klima, Energie und Agrarwirtschaft](https://www.hamburg.de/bukea/)                                                                           |
| Hessen                 | [Hessisches Landesamt für Naturschutz, Umwelt und Geologie](https://lgd.hessen.de/mapapps/resources/apps/lgd/index.html?lang=de)                                  |
| Mecklenburg-Vorpommern | [Mecklenburg-Vorpommern Landesamt für Umwelt, Naturschutz und Geologie](https://www.umweltkarten.mv-regierung.de/script/)                                         |
| Niedersachsen          | [Niedersächsischer Landesbetrieb für Wasserwirtschaft, Küsten- und Naturschutz](https://www.nlwkn.niedersachsen.de/startseite/)                                   |
| Nordrhein-Westfalen    | [Landesamt für Natur, Umwelt und Verbraucherschutz NRW](https://www.lanuv.nrw.de/)                                                                                |
| Rheinland-Pfalz        | [Ministerium für Klimaschutz, Umwelt, Energie und Mobilität des Landes Rheinland-Pfalz ](https://wasserportal.rlp-umwelt.de/servlet/is/1632/)                     |
| Saarland               | [Ministerium für Umwelt, Klima, Mobilität, Agrar und Verbraucherschutz](https://www.saarland.de/mukmav/DE/home/home_node.html)                                    |
| Sachsen                | [Landesamt für Umwelt, Landwirtscahft und Geologie ](https://www.umwelt.sachsen.de/umwelt/infosysteme/ida/)                                                       |
| Sachsen-Anhalt         | [Landesbetrieb für Hochwasserschutz und Wasserwirtschaft Sachsen-Anhalt](https://gld-sa.dhi-wasy.de/GLD-Portal/)                                                  |
| Schleswig-Holstein     | [Landesamt für Landwirtschaft, Umwelt und ländliche Räume](https://www.schleswig-holstein.de/DE/landesregierung/ministerien-behoerden/LLUR/llur_node.html)        |
| Thüringen              | [Thüringer Landesamt für Umwelt, Bergbau und Naturschutz](https://antares.thueringen.de/cadenza/;jsessionid=558E43B4468DB2CA8AAE1D4D9C435C27)                     |

## Über CORRECTIV.Lokal

Dieses Projekt wurde von [CORRECTIV.Lokal](https://correctiv.org/lokal/).

CORRECTIV.Lokal stößt Recherchen in Themenfeldern an, die eine nationale Bedeutung haben und gleichzeitig vor Ort relevant sind. Dabei arbeiten wir in einem landesweiten Netzwerk mit mehr als 1.000 Lokaljournalistinnen und -journalisten zusammen. CORRECTIV.Lokal arbeitet gemeinnützig. Alle Angebote und Services sind kostenfrei.

Wenn Sie Mitglied des Netzwerks werden und sich an der zukünftigen Recherche beteiligen möchten, können Sie sich [hier](https://correctiv.org/lokal/) anmelden.

## Nennung

Bei der Verwendung dieser Daten, beachten Sie bitte die Regeln zur [Nennung von CORRECTIV.Lokal](https://docs.google.com/document/d/1qNWGf72hdjkJh2nBjvQXoAc7Flv-fkw9urfKe_PzZ9A/edit?usp=sharing).
