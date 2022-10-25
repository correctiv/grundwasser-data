# Grundwasser Daten

Das Grundwasser in Deutschland sinkt dramatisch. CORRECTIV hat erstmals für Daten von rund 6.700 Grundwassermessstellen einen Überblick erstellt und sie im Vergleich ausgewertet.

Die Analyse liefert erschreckende Befunde: An knapp der Hälfte aller ausgewerteten Messstellen ist das Grundwasser in den Dürrejahren zwischen 2018 und 2021 auf den tiefsten Stand seit 1990 gefallen. Insgesamt ist in den vergangenen 32 Jahren der Grundwasserstand mehr gesunken als gestiegen.

Lesen Sie hier unsere Recherche: [Wo in Deutschland das Grundwasser sinkt](tk_link)

## Methodik

Grundlage unserer Analyse sind Rohdaten von Grundwassermessstellen aus 13 Bundesländern. Wir haben die Daten durch eine Kombination aus Scraping, also dem Herunterladen von Daten von den Webseiten der Behörden, und Presseanfragen gesammelt. Die von uns ausgewerteten Grundwassermessstellen liegen in unterschiedlichen Tiefen.

In einigen Bundesländern werden die Grundwasserstände täglich gemessen. In anderen wird jede zweite Woche oder einmal im Monat gemessen. Um die Daten zu normalisieren, haben wir für jede Messstelle den durchschnittlichen Grundwasserstand für jeden Monat zwischen 1990 und 2021 berechnet. Wenn für eine Messstelle weniger als 95 Prozent der Monatsdaten zur Verfügung standen, haben wir sie aus unserer Analyse ausgeschlossen. In Bremen, Hamburg und dem Saarland war nach diesem Kriterium die Datenlage nicht ausreichend für unsere Auswertung.

Zusätzlich führten wir einen halbautomatischen Test auf Datengenauigkeit durch. Dafür haben wir das [Changepoint package](https://rdrr.io/cran/changepoint/man/cpt.mean.html) in R verwendet. Wir untersuchten für jede Messstelle sprunghafte Veränderungen im Werteverlauf, was auf Fehler in den Daten hindeuten kann. Wenn beispielsweise eine Messstelle ausgetauscht wird und die Messungen nicht für die neue Höhe neu kalibriert werden, kann es den Anschein haben, dass der Grundwasserspiegel plötzlich gestiegen ist. Anschließend haben wir die Daten für jede Messstelle, bei der solche Sprünge identifiziert wurden, visuell überprüft und die fehlerhaften Stationen aus unserer Analyse entfernt.

Schließlich berechneten wir den 32-Jahres-Trend mithilfe des Mann-Kendall-Trendtests, der auf langfristig steigende oder fallende Trends prüft. Wir haben die [Trend-Free Pre-Whitening-Variante](https://rdrr.io/cran/modifiedmk/man/tfpwmk.html) des Tests verwendet. Die Analyse ergab den Trend als Veränderung in Metern pro Monat. Um die Daten zu normalisieren, teilten wir dann für jede Messstelle den Trend durch den Werteumfang der Messstelle (Differenz zwischen dem höchsten und dem niedrigsten Monatsmittelwert) und rechneten ihn auf ein Jahr hoch, um den Trend in Prozent pro Jahr ausdrücken zu können.

Die Trends werden wie folgt klassifiziert:

Trend                                         | Klassifizierung    |
--------------------------------------------- | ------------------ |
niedriger als -1 Prozent pro Jahr             | stark sinkend      |
-1 bis -0,5 Prozent pro Jahr                  | leicht sinkend     |
-0,5 bis 0,5 Prozent pro Jahr | kein starker Trend |
0,5 bis 1 Prozent pro Jahr                    | leicht steigend    |
stärker als 1 Prozent pro Jahr                | stark steigend     |

Wir haben [diese Auswertung](https://www.google.com/url?q=https://www.umwelt.niedersachsen.de/startseite/themen/wasser/grundwasser/grundwasserbericht_niedersachsen/grundwassermenge/einfuhrung/trendanalyse/auswertungen-105741.html&sa=D&source=docs&ust=1666630468290243&usg=AOvVaw3XjlZDnmTe6a3QWsaA7UcD) des Niedersächsischen Ministeriums für Umwelt, Energie, Bauen und Klimaschutz als Orientierung genutzt.

Eine Beispielanalyse für ausgewählte Messstationen finden Sie in [trendanalyse.ipynb](trendanalyse.ipynb).

Wir haben auch berechnet, in welchen Jahren jede Messstelle ihren Tiefststand erreicht hat, und die Daten nach Kreisen zusammengefasst. Ein Beispiel für die Berechnung finden Sie unter [tiefststaende.ipynb](tiefststaende.ipynb).

## Die Daten

Hier stellen wir die bundesweiten Stammdaten aller Messstellen zur Verfügung. Wir haben insgesamt von 23.805 Messstellen Daten bekommen und konnten davon für unsere weitere Auswertung 6.677 Messstellen nutzen. Das lag teilweise an fehlenden Messungen oder auch daran, dass wir Daten anonymisiert (ohne Koordinaten) erhalten haben. Wenn die Datenlage ausreichend war, haben wir unsere 32-jährige Trendanalyse durchgeführt und stellen diese Ergebnisse zur Verfügung.

Wir veröffentlichen auch die monatlichen Tiefst-, Mittel- und Höchstwerte für jede Messstelle für jeden Monat zwischen 1990 und 2021. Außerdem können Sie auch Daten über die zeitliche Entwicklung der tiefsten Wasserstände nach Kreisen abrufen.

In allen Dateien verwenden wir einen Punkt "." für Dezimalstellen.

### Stammdaten und Trends ([trends.csv](trends.csv))

Ein Beispiel dafür, wie Sie diese Werte selbst berechnen können, finden Sie in [trendanalyse.ipynb](trendanalyse.ipynb).

- bundesland (Bundesland, in dem die Messstelle liegt)
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

### Monatliche Daten ([monthly](monthly/))

Dieser Ordner enthält monatliche Grundwasserdaten nach Bundesländern.

- ms_nr
- year
- month
- min_gwl (in Metern)
- mean_gwl (in Metern)
- max_gwl (in Metern)

### Tiefststände ([tiefststaende_nach_kreis.csv](tiefststaende_nach_kreis.csv))

Unter jeder Jahresspalte steht die Gesamtzahl der Messstationen in einem bestimmten Jahr, die ihren Tiefststand in diesem Jahr erreicht haben. Einige der Messstellen verzeichnen über mehrere Jahre einen Tiefststand. Ein Beispiel dafür, wie Sie diese Werte selbst berechnen können, finden Sie in [tiefstaende.ipynb](tiefstaende.ipynb).

- bundesland
- kreis
- BEZ
- AGS
- jahr (1990-2021)
- messstellen_insgesamt

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

Diese Erhebung und Auswertung der Daten von Grundwasssermessstellen stammt von [CORRECTIV.Lokal](https://correctiv.org/lokal/).

CORRECTIV.Lokal gehört zur gemeinnützigen Redaktion von CORRECTIV und ist ein Netzwerk von mehr als 1.000 Lokaljournalistinnen und -journalisten aus ganz Deutschland. Alle Angebote und Services sind kostenfrei. Wenn Sie im Lokaljournalismus arbeiten und Mitglied des Netzwerks werden möchten, können Sie sich [hier](https://correctiv.org/lokal/) anmelden.

## Diese Daten nutzen

Beachten Sie bitte die Regeln zur [Nennung von CORRECTIV.Lokal](https://docs.google.com/document/d/1qNWGf72hdjkJh2nBjvQXoAc7Flv-fkw9urfKe_PzZ9A/edit?usp=sharing), wenn Sie die Daten verwenden möchten.
