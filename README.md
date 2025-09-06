# Clickhouse-test-task
Тестове завдання на основі датасету:  https://www.kaggle.com/datasets/oles04/bundesliga-soccer-player

Переважний діалект SQL для вирішення задачі - ClickHouse

1) Вивести топ-3 клуби із найдорожчим захистом (Defender-*)
2) У розрізі клубу та гравця порахувати, скільки гравців підписало контракт із клубом після нього
3) Вибрати клуби, де середня вартість французьких гравців більша за 5 млн
4) Вибрати клуби, де частка німців вища за 90%
5) Вибрати найдорожчого у своєму віці (на виході ім'я + вартість)
6) Вибрати гравців, з вартістю у 1.5 рази більше, ніж у середньому за своєю позицією
7) На якій позиції найважче отримати контракт з будь-якою компанією
8) Порахувати, в якій команді найперше закінчиться контракт у 5 гравців
9) У якому віці гравці здебільшого виходять на пік своєї вартості
10) У якої команди найзіграніший склад (найдовше грають разом)
11) У яких командах є тезки
12) Вивести команди, де топ-3 гравці займають 50% платіжної відомості


# Bundesliga Players – ClickHouse Test Task

## Джерело даних
Датасет з Kaggle:  
[Bundesliga Soccer Player Dataset](https://www.kaggle.com/datasets/oles04/bundesliga-soccer-player)

---

##  Завдання та рішення

### 1) Топ-3 клуби з найдорожчим захистом
```sql
SELECT 
    club
    , SUM(price) AS total_defence_value
FROM bundesliga_player
WHERE position LIKE 'Defender -%'
GROUP BY club
ORDER BY total_defence_value DESC
LIMIT 3;

Результат:

| club          | total_defence_value |
|---------------|---------------------|
| Bayern Munich | 398.5               |
| RB Leipzig    | 179.5               |
| B. Leverkusen | 145                 |
```
### 2) У розрізі клубу та гравця порахувати, скільки гравців підписало контракт із клубом після нього
```sql
SELECT
    bp1.club
    , bp1.name
    , COUNT() AS players_after
FROM bundesliga_player AS bp1
JOIN bundesliga_player AS bp2
  ON bp1.club = bp2.club
 AND bp2.joined_club > bp1.joined_club
GROUP BY bp1.club, bp1.name
ORDER BY bp1.club, players_after DESC;

Результат:

| club            | name                     | players_after |
|-----------------|--------------------------|---------------|
| 1.FC Köln       | Timo Horn                | 26            |
| 1.FC Köln       | Jonas Hector             | 25            |
| 1.FC Köln       | Benno Schmitz            | 24            |
| 1.FC Köln       | Florian Kainz            | 23            |
| 1.FC Köln       | Kingsley Schindler       | 22            |
| 1.FC Köln       | Ellyes Skhiri            | 21            |
| 1.FC Köln       | Jan Thielmann            | 20            |
| 1.FC Köln       | Tim Lemperle             | 19            |
| 1.FC Köln       | Sebastian Andersson      | 18            |
| 1.FC Köln       | Dimitrios Limnios        | 17            |
| 1.FC Köln       | Marvin Schwäbe           | 13            |
| 1.FC Köln       | Mark Uth                 | 13            |
| 1.FC Köln       | Timo Hübers              | 13            |
| 1.FC Köln       | Dejan Ljubicic           | 13            |
| 1.FC Köln       | Julian Chabot            | 12            |
| 1.FC Köln       | Mathias Olesen           | 11            |
| 1.FC Köln       | Matthias Köbbing         | 10            |
| 1.FC Köln       | Kristian Pedersen        | 3             |
| 1.FC Köln       | Steffen Tigges           | 3             |
| 1.FC Köln       | Denis Huseinbasic        | 3             |
| 1.FC Köln       | Florian Dietz            | 3             |
| 1.FC Köln       | Luca Kilian              | 3             |
| 1.FC Köln       | Linton Maina             | 3             |
| 1.FC Köln       | Eric Martel              | 3             |
| 1.FC Köln       | Sargis Adamyan           | 2             |
| 1.FC Köln       | Nikola Soldo             | 1             |
| 1.FC Köln II    | Georg Strauch            | 4             |
| 1.FC Köln II    | Joshua Schwirten         | 3             |
| 1.FC Köln II    | Rijad Smajic             | 2             |
| 1.FC Köln II    | Maximilian Schmid        | 1             |
| 1.FSV Mainz 05  | Stefan Bell              | 26            |
| 1.FSV Mainz 05  | Robin Zentner            | 25            |
| 1.FSV Mainz 05  | Alexander Hack           | 24            |
| 1.FSV Mainz 05  | Karim Onisiwo            | 23            |
| 1.FSV Mainz 05  | Leandro Barreiro         | 20            |
| 1.FSV Mainz 05  | Finn Dahmen              | 20            |
| 1.FSV Mainz 05  | Jonathan Burkardt        | 20            |
| 1.FSV Mainz 05  | Aarón Martín             | 18            |
| 1.FSV Mainz 05  | Edimilson Fernandes      | 18            |
| 1.FSV Mainz 05  | Marlon Mustapha          | 17            |
| 1.FSV Mainz 05  | Lasse Rieß               | 16            |
| 1.FSV Mainz 05  | Jae-sung Lee             | 15            |
| 1.FSV Mainz 05  | Silvan Widmer            | 14            |
| 1.FSV Mainz 05  | Anton Stach              | 13            |
| 1.FSV Mainz 05  | Eniss Shabani            | 3             |
| 1.FSV Mainz 05  | Anthony Caci             | 3             |
| 1.FSV Mainz 05  | Danny da Costa           | 3             |
| 1.FSV Mainz 05  | Nelson Weiper            | 3             |
| 1.FSV Mainz 05  | Maxim Leitsch            | 3             |
| 1.FSV Mainz 05  | Marcus Ingvartsen        | 3             |
| 1.FSV Mainz 05  | Philipp Schulz           | 3             |
| 1.FSV Mainz 05  | Aymen Barkok             | 3             |
| 1.FSV Mainz 05  | Delano Burgzorg          | 3             |
| 1.FSV Mainz 05  | Dominik Kohr             | 3             |
| 1.FSV Mainz 05  | Brajan Gruda             | 2             |
| 1.FSV Mainz 05  | Andreas Hanche-Olsen     | 1             |
| B. Dortmund II  | Antonios Papadopoulos    | 2             |
| B. Dortmund II  | Abdoulaye Kamara         | 1             |
| B. Leverkusen   | Karim Bellarabi          | 26            |
| B. Leverkusen   | Niklas Lomb              | 25            |
| B. Leverkusen   | Jonathan Tah             | 24            |
| B. Leverkusen   | Lukas Hradecky           | 23            |
| B. Leverkusen   | Daley Sinkgraven         | 20            |
| B. Leverkusen   | Moussa Diaby             | 20            |
| B. Leverkusen   | Kerem Demirbay           | 20            |
| B. Leverkusen   | Nadiem Amiri             | 19            |
| B. Leverkusen   | Exequiel Palacios        | 18            |
| B. Leverkusen   | Edmond Tapsoba           | 17            |
| B. Leverkusen   | Ayman Azhil              | 15            |
| B. Leverkusen   | Florian Wirtz            | 15            |
| B. Leverkusen   | Patrik Schick            | 14            |
| B. Leverkusen   | Timothy Fosu-Mensah      | 13            |
| B. Leverkusen   | Jeremie Frimpong         | 12            |
| B. Leverkusen   | Andrey Lunev             | 11            |
| B. Leverkusen   | Mitchel Bakker           | 10            |
| B. Leverkusen   | Odilon Kossounou         | 9             |
| B. Leverkusen   | Robert Andrich           | 7             |
| B. Leverkusen   | Piero Hincapié           | 7             |
| B. Leverkusen   | Amine Adli               | 6             |
| B. Leverkusen   | Sardar Azmoun            | 5             |
| B. Leverkusen   | Joshua Eze               | 3             |
| B. Leverkusen   | Adam Hlozek              | 3             |
| B. Leverkusen   | Callum Hudson-Odoi       | 2             |
| B. Leverkusen   | Noah Mbamba              | 1             |
| Bayern Munich   | Thomas Müller            | 26            |
| Bayern Munich   | Manuel Neuer             | 25            |
| Bayern Munich   | Joshua Kimmich           | 24            |
| Bayern Munich   | Kingsley Coman           | 22            |
| Bayern Munich   | Serge Gnabry             | 22            |
| Bayern Munich   | Leon Goretzka            | 21            |
| Bayern Munich   | Alphonso Davies          | 20            |
| Bayern Munich   | Benjamin Pavard          | 18            |
| Bayern Munich   | Lucas Hernández          | 18            |
| Bayern Munich   | Jamal Musiala            | 17            |
| Bayern Munich   | Leroy Sané               | 16            |
| Bayern Munich   | Eric Maxim Choupo-Moting | 14            |
| Bayern Munich   | Bouna Sarr               | 14            |
| Bayern Munich   | Sven Ulreich             | 13            |
| Bayern Munich   | Dayot Upamecano          | 12            |
| Bayern Munich   | Josip Stanisic           | 11            |
| Bayern Munich   | Paul Wanner              | 10            |
| Bayern Munich   | Johannes Schenk          | 6             |
| Bayern Munich   | Noussair Mazraoui        | 6             |
| Bayern Munich   | Ryan Gravenberch         | 6             |
| Bayern Munich   | Sadio Mané               | 6             |
| Bayern Munich   | Matthijs de Ligt         | 5             |
| Bayern Munich   | Mathys Tel               | 4             |
| Bayern Munich   | Daley Blind              | 3             |
| Bayern Munich   | Arijon Ibrahimovic       | 2             |
| Bayern Munich   | Yann Sommer              | 1             |
| Bor. Dortmund   | Marco Reus               | 29            |
| Bor. Dortmund   | Felix Passlack           | 28            |
| Bor. Dortmund   | Raphaël Guerreiro        | 27            |
| Bor. Dortmund   | Mahmoud Dahoud           | 26            |
| Bor. Dortmund   | Marius Wolf              | 25            |
| Bor. Dortmund   | Mateu Morey Bauzà        | 20            |
| Bor. Dortmund   | Nico Schulz              | 20            |
| Bor. Dortmund   | Luca Unbehaun            | 20            |
| Bor. Dortmund   | Julian Brandt            | 20            |
| Bor. Dortmund   | Mats Hummels             | 20            |
| Bor. Dortmund   | Giovanni Reyna           | 19            |
| Bor. Dortmund   | Emre Can                 | 17            |
| Bor. Dortmund   | Thomas Meunier           | 17            |
| Bor. Dortmund   | Jude Bellingham          | 16            |
| Bor. Dortmund   | Youssoufa Moukoko        | 15            |
| Bor. Dortmund   | Gregor Kobel             | 13            |
| Bor. Dortmund   | Soumaïla Coulibaly       | 13            |
| Bor. Dortmund   | Donyell Malen            | 12            |
| Bor. Dortmund   | Jamie Bynoe-Gittens      | 4             |
| Bor. Dortmund   | Alexander Meyer          | 4             |
| Bor. Dortmund   | Tom Rothe                | 4             |
| Bor. Dortmund   | Niklas Süle              | 4             |
| Bor. Dortmund   | Salih Özcan              | 4             |
| Bor. Dortmund   | Marcel Lotka             | 4             |
| Bor. Dortmund   | Karim Adeyemi            | 4             |
| Bor. Dortmund   | Nico Schlotterbeck       | 4             |
| Bor. Dortmund   | Sébastien Haller         | 3             |
| Bor. Dortmund   | Anthony Modeste          | 2             |
| Bor. Dortmund   | Julian Ryerson           | 1             |
| Bor. M'gladbach | Tony Jantschke           | 26            |
| Bor. M'gladbach | Patrick Herrmann         | 25            |
| Bor. M'gladbach | Tobias Sippel            | 22            |
| Bor. M'gladbach | Nico Elvedi              | 22            |
| Bor. M'gladbach | Lars Stindl              | 22            |
| Bor. M'gladbach | Jonas Hofmann            | 21            |
| Bor. M'gladbach | Mamadou Doucouré         | 19            |
| Bor. M'gladbach | Christoph Kramer         | 19            |
| Bor. M'gladbach | Florian Neuhaus          | 18            |
| Bor. M'gladbach | Alassane Plea            | 17            |
| Bor. M'gladbach | Stefan Lainer            | 16            |
| Bor. M'gladbach | Marcus Thuram            | 15            |
| Bor. M'gladbach | Ramy Bensebaini          | 14            |
| Bor. M'gladbach | Jan Olschowsky           | 13            |
| Bor. M'gladbach | Joe Scally               | 12            |
| Bor. M'gladbach | Manu Koné                | 11            |
| Bor. M'gladbach | Conor Noß                | 9             |
| Bor. M'gladbach | Hannes Wolf              | 9             |
| Bor. M'gladbach | Luca Netz                | 8             |
| Bor. M'gladbach | Marvin Friedrich         | 7             |
| Bor. M'gladbach | Oscar Fraulo             | 5             |
| Bor. M'gladbach | Yvandro Borges Sanches   | 5             |
| Bor. M'gladbach | Ko Itakura               | 4             |
| Bor. M'gladbach | Nathan Ngoumou           | 3             |
| Bor. M'gladbach | Julian Weigl             | 2             |
| Bor. M'gladbach | Simon Walde              | 1             |
| E. Frankfurt    | Makoto Hasebe            | 28            |
| E. Frankfurt    | Timothy Chandler         | 28            |
| E. Frankfurt    | Daichi Kamada            | 27            |
| E. Frankfurt    | Evan Ndicka              | 26            |
| E. Frankfurt    | Almamy Touré             | 24            |
| E. Frankfurt    | Tuta                     | 24            |
| E. Frankfurt    | Djibril Sow              | 23            |
| E. Frankfurt    | Sebastian Rode           | 22            |
| E. Frankfurt    | Kevin Trapp              | 21            |
| E. Frankfurt    | Diant Ramaj              | 19            |
| E. Frankfurt    | Christopher Lenz         | 19            |
| E. Frankfurt    | Rafael Borré             | 18            |
| E. Frankfurt    | Jesper Lindström         | 17            |
| E. Frankfurt    | Jens Grahl               | 16            |
| E. Frankfurt    | Jan Schröder             | 15            |
| E. Frankfurt    | Ansgar Knauff            | 14            |
| E. Frankfurt    | Hrvoje Smolcic           | 5             |
| E. Frankfurt    | Lucas Alario             | 5             |
| E. Frankfurt    | Mario Götze              | 5             |
| E. Frankfurt    | Mehdi Loune              | 5             |
| E. Frankfurt    | Faride Alidou            | 5             |
| E. Frankfurt    | Aurélio Buta             | 5             |
| E. Frankfurt    | Randal Kolo Muani        | 5             |
| E. Frankfurt    | Marcel Wenig             | 5             |
| E. Frankfurt    | Kristijan Jakic          | 5             |
| E. Frankfurt    | Junior Dina Ebimbe       | 4             |
| E. Frankfurt    | Nacho Ferri              | 3             |
| E. Frankfurt    | Simon Simoni             | 1             |
| E. Frankfurt    | Paxten Aaronson          | 1             |
| FC Augsburg     | Jeffrey Gouweleeuw       | 30            |
| FC Augsburg     | Fredrik Jensen           | 28            |
| FC Augsburg     | André Hahn               | 28            |
| FC Augsburg     | Benjamin Leneis          | 27            |
| FC Augsburg     | Mads Pedersen            | 23            |
| FC Augsburg     | Noah Joel Sarenren Bazee | 23            |
| FC Augsburg     | Iago                     | 23            |
| FC Augsburg     | Rubén Vargas             | 23            |
| FC Augsburg     | Reece Oxford             | 22            |
| FC Augsburg     | Tomas Koubek             | 21            |
| FC Augsburg     | Tobias Strobl            | 17            |
| FC Augsburg     | Felix Uduokhai           | 17            |
| FC Augsburg     | Daniel Caligiuri         | 17            |
| FC Augsburg     | Rafal Gikiewicz          | 17            |
| FC Augsburg     | Robert Gumny             | 16            |
| FC Augsburg     | Daniel Klein             | 15            |
| FC Augsburg     | Niklas Dorsch            | 14            |
| FC Augsburg     | Aaron Zehnter            | 11            |
| FC Augsburg     | Maximilian Bauer         | 11            |
| FC Augsburg     | Arne Maier               | 11            |
| FC Augsburg     | Ermedin Demirovic        | 10            |
| FC Augsburg     | Elvis Rexhbecaj          | 9             |
| FC Augsburg     | Julian Baumgartlinger    | 8             |
| FC Augsburg     | Mergim Berisha           | 7             |
| FC Augsburg     | Arne Engels              | 6             |
| FC Augsburg     | Dion Beljo               | 5             |
| FC Augsburg     | David Colina             | 4             |
| FC Augsburg     | Irvin Cardona            | 2             |
| FC Augsburg     | Kelvin Yeboah            | 2             |
| FC Augsburg     | Nathanaël Mbuku          | 1             |
| FC Schalke 04   | Ralf Fährmann            | 30            |
| FC Schalke 04   | Michael Langer           | 29            |
| FC Schalke 04   | Marius Bülter            | 24            |
| FC Schalke 04   | Mehmet Aydin             | 24            |
| FC Schalke 04   | Danny Latza              | 24            |
| FC Schalke 04   | Marcin Kaminski          | 24            |
| FC Schalke 04   | Simon Terodde            | 24            |
| FC Schalke 04   | Dominick Drexler         | 23            |
| FC Schalke 04   | Henning Matriciani       | 22            |
| FC Schalke 04   | Rodrigo Zalazar          | 13            |
| FC Schalke 04   | Justin Heekeren          | 13            |
| FC Schalke 04   | Ibrahima Cissé           | 13            |
| FC Schalke 04   | Alexander Schwolow       | 13            |
| FC Schalke 04   | Leo Greiml               | 13            |
| FC Schalke 04   | Tom Krauß                | 13            |
| FC Schalke 04   | Tobias Mohr              | 13            |
| FC Schalke 04   | Sebastian Polter         | 13            |
| FC Schalke 04   | Thomas Ouwejan           | 13            |
| FC Schalke 04   | Maya Yoshida             | 12            |
| FC Schalke 04   | Cédric Brunner           | 11            |
| FC Schalke 04   | Alex Kral                | 10            |
| FC Schalke 04   | Sepp van den Berg        | 9             |
| FC Schalke 04   | Kenan Karaman            | 8             |
| FC Schalke 04   | Timothée Kolodziejczak   | 7             |
| FC Schalke 04   | Niklas Tauer             | 5             |
| FC Schalke 04   | Soichiro Kozuki          | 5             |
| FC Schalke 04   | Jere Uronen              | 4             |
| FC Schalke 04   | Michael Frey             | 3             |
| FC Schalke 04   | Tim Skarke               | 2             |
| FC Schalke 04   | Moritz Jenz              | 1             |
| Hertha BSC      | Peter Pekarik            | 26            |
| Hertha BSC      | Rune Jarstein            | 25            |
| Hertha BSC      | Marvin Plattenhardt      | 24            |
| Hertha BSC      | Maximilian Mittelstädt   | 23            |
| Hertha BSC      | Dodi Lukébakio           | 22            |
| Hertha BSC      | Lucas Tousart            | 21            |
| Hertha BSC      | Márton Dárdai            | 20            |
| Hertha BSC      | Suat Serdar              | 18            |
| Hertha BSC      | Kevin-Prince Boateng     | 18            |
| Hertha BSC      | Stevan Jovetic           | 17            |
| Hertha BSC      | Marco Richter            | 16            |
| Hertha BSC      | Oliver Christensen       | 15            |
| Hertha BSC      | Marc Oliver Kempf        | 14            |
| Hertha BSC      | Kelian Nsona             | 13            |
| Hertha BSC      | Filip Uremovic           | 9             |
| Hertha BSC      | Derry Scherhant          | 9             |
| Hertha BSC      | Julian Eitschberger      | 9             |
| Hertha BSC      | Jonjoe Kenny             | 9             |
| Hertha BSC      | Jessic Ngankam           | 8             |
| Hertha BSC      | Ivan Sunjic              | 7             |
| Hertha BSC      | Chidera Ejuke            | 6             |
| Hertha BSC      | Wilfried Kanga           | 5             |
| Hertha BSC      | Jean-Paul Boëtius        | 4             |
| Hertha BSC      | Agustín Rogel            | 3             |
| Hertha BSC      | Tjark Ernst              | 2             |
| Hertha BSC      | Florian Niederlechner    | 1             |
| RB Leipzig      | Yussuf Poulsen           | 24            |
| RB Leipzig      | Lukas Klostermann        | 23            |
| RB Leipzig      | Emil Forsberg            | 22            |
| RB Leipzig      | Péter Gulácsi            | 20            |
| RB Leipzig      | Willi Orbán              | 20            |
| RB Leipzig      | Marcel Halstenberg       | 19            |
| RB Leipzig      | Konrad Laimer            | 18            |
| RB Leipzig      | Kevin Kampl              | 17            |
| RB Leipzig      | Amadou Haidara           | 16            |
| RB Leipzig      | Christopher Nkunku       | 15            |
| RB Leipzig      | Dani Olmo                | 14            |
| RB Leipzig      | Dominik Szoboszlai       | 13            |
| RB Leipzig      | Josko Gvardiol           | 9             |
| RB Leipzig      | Caden Clark              | 9             |
| RB Leipzig      | Benjamin Henrichs        | 9             |
| RB Leipzig      | Mohamed Simakan          | 9             |
| RB Leipzig      | André Silva              | 8             |
| RB Leipzig      | Oskar Preil              | 4             |
| RB Leipzig      | Sanoussy Ba              | 4             |
| RB Leipzig      | Janis Blaswich           | 4             |
| RB Leipzig      | Xaver Schlager           | 4             |
| RB Leipzig      | David Raum               | 3             |
| RB Leipzig      | Timo Werner              | 2             |
| RB Leipzig      | Abdou Diallo             | 1             |
| SC Freiburg     | Nicolas Höfler           | 25            |
| SC Freiburg     | Christian Günter         | 24            |
| SC Freiburg     | Lukas Kübler             | 22            |
| SC Freiburg     | Nils Petersen            | 22            |
| SC Freiburg     | Manuel Gulde             | 21            |
| SC Freiburg     | Lucas Höler              | 20            |
| SC Freiburg     | Mark Flekken             | 18            |
| SC Freiburg     | Philipp Lienhart         | 18            |
| SC Freiburg     | Roland Sallai            | 17            |
| SC Freiburg     | Woo-yeong Jeong          | 15            |
| SC Freiburg     | Jonathan Schmid          | 15            |
| SC Freiburg     | Vincenzo Grifo           | 14            |
| SC Freiburg     | Yannik Keitel            | 13            |
| SC Freiburg     | Benjamin Uphoff          | 12            |
| SC Freiburg     | Kimberly Ezekwem         | 8             |
| SC Freiburg     | Kiliann Sildillia        | 8             |
| SC Freiburg     | Noah Atubolu             | 8             |
| SC Freiburg     | Noah Weißhaupt           | 8             |
| SC Freiburg     | Maximilian Eggestein     | 7             |
| SC Freiburg     | Daniel-Kofi Kyereh       | 4             |
| SC Freiburg     | Robert Wagner            | 4             |
| SC Freiburg     | Matthias Ginter          | 4             |
| SC Freiburg     | Ritsu Doan               | 3             |
| SC Freiburg     | Michael Gregoritsch      | 2             |
| SC Freiburg     | Merlin Röhl              | 1             |
| TSG Hoffenheim  | Kevin Akpoguma           | 30            |
| TSG Hoffenheim  | Ermin Bicakcic           | 28            |
| TSG Hoffenheim  | Oliver Baumann           | 28            |
| TSG Hoffenheim  | Pavel Kaderabek          | 27            |
| TSG Hoffenheim  | Kevin Vogt               | 24            |
| TSG Hoffenheim  | Andrej Kramaric          | 24            |
| TSG Hoffenheim  | Dennis Geiger            | 24            |
| TSG Hoffenheim  | Christoph Baumgartner    | 23            |
| TSG Hoffenheim  | Philipp Pentke           | 21            |
| TSG Hoffenheim  | Ihlas Bebou              | 21            |
| TSG Hoffenheim  | Robert Skov              | 20            |
| TSG Hoffenheim  | Munas Dabbur             | 19            |
| TSG Hoffenheim  | Jacob Bruun Larsen       | 18            |
| TSG Hoffenheim  | Luca Philipp             | 17            |
| TSG Hoffenheim  | Tom Bischof              | 13            |
| TSG Hoffenheim  | Sebastian Rudy           | 13            |
| TSG Hoffenheim  | Angelo Stiller           | 13            |
| TSG Hoffenheim  | Nahuel Noll              | 13            |
| TSG Hoffenheim  | Fisnik Asllani           | 12            |
| TSG Hoffenheim  | Muhammed Damar           | 9             |
| TSG Hoffenheim  | Grischa Prömel           | 9             |
| TSG Hoffenheim  | Finn Ole Becker          | 9             |
| TSG Hoffenheim  | Ozan Kabak               | 8             |
| TSG Hoffenheim  | Stanley Nsoki            | 7             |
| TSG Hoffenheim  | Eduardo Quaresma         | 6             |
| TSG Hoffenheim  | Angeliño                 | 5             |
| TSG Hoffenheim  | Kasper Dolberg           | 4             |
| TSG Hoffenheim  | Justin Che               | 2             |
| TSG Hoffenheim  | Umut Tohumcu             | 2             |
| TSG Hoffenheim  | John Anthony Brooks      | 1             |
| Union Berlin    | Christopher Trimmel      | 26            |
| Union Berlin    | Jakob Busk               | 25            |
| Union Berlin    | Sheraldo Becker          | 24            |
| Union Berlin    | Tim Maciejewski          | 23            |
| Union Berlin    | Niko Gießelmann          | 22            |
| Union Berlin    | Robin Knoche             | 21            |
| Union Berlin    | Paul Jaeckel             | 16            |
| Union Berlin    | Levin Öztunali           | 16            |
| Union Berlin    | Kevin Behrens            | 16            |
| Union Berlin    | Rani Khedira             | 16            |
| Union Berlin    | Timo Baumgartl           | 16            |
| Union Berlin    | Frederik Rönnow          | 15            |
| Union Berlin    | Kevin Möhwald            | 14            |
| Union Berlin    | András Schäfer           | 13            |
| Union Berlin    | Sven Michel              | 12            |
| Union Berlin    | Lennart Grill            | 5             |
| Union Berlin    | Paul Seguin              | 5             |
| Union Berlin    | Jordan                   | 5             |
| Union Berlin    | Danilho Doekhi           | 5             |
| Union Berlin    | Janik Haberer            | 5             |
| Union Berlin    | Jamie Leweling           | 5             |
| Union Berlin    | Milos Pantovic           | 5             |
| Union Berlin    | Diogo Leite              | 4             |
| Union Berlin    | Morten Thorsby           | 3             |
| Union Berlin    | Jérôme Roussillon        | 2             |
| Union Berlin    | Josip Juranovic          | 1             |
| VfB Stuttgart   | Borna Sosa               | 28            |
| VfB Stuttgart   | Atakan Karazor           | 26            |
| VfB Stuttgart   | Fabian Bredlow           | 26            |
| VfB Stuttgart   | Tanguy Coulibaly         | 25            |
| VfB Stuttgart   | Silas                    | 24            |
| VfB Stuttgart   | Nikolas Nartey           | 23            |
| VfB Stuttgart   | Lilian Egloff            | 20            |
| VfB Stuttgart   | Wataru Endo              | 20            |
| VfB Stuttgart   | Pascal Stenzel           | 20            |
| VfB Stuttgart   | Antonis Aidonis          | 19            |
| VfB Stuttgart   | Waldemar Anton           | 18            |
| VfB Stuttgart   | Florian Müller           | 16            |
| VfB Stuttgart   | Ömer Beyaz               | 16            |
| VfB Stuttgart   | Chris Führich            | 15            |
| VfB Stuttgart   | Enzo Millot              | 14            |
| VfB Stuttgart   | Florian Schock           | 13            |
| VfB Stuttgart   | Tiago Tomás              | 12            |
| VfB Stuttgart   | Hiroki Ito               | 7             |
| VfB Stuttgart   | Thomas Kastanaras        | 7             |
| VfB Stuttgart   | Laurin Ulrich            | 7             |
| VfB Stuttgart   | Alou Kuol                | 7             |
| VfB Stuttgart   | Konstantinos Mavropanos  | 7             |
| VfB Stuttgart   | Juan José Perea          | 6             |
| VfB Stuttgart   | Josha Vagnoman           | 5             |
| VfB Stuttgart   | Luca Pfeiffer            | 4             |
| VfB Stuttgart   | Serhou Guirassy          | 3             |
| VfB Stuttgart   | Dan-Axel Zagadou         | 2             |
| VfL Bochum      | Anthony Losilla          | 27            |
| VfL Bochum      | Manuel Riemann           | 26            |
| VfL Bochum      | Danilo Soares            | 25            |
| VfL Bochum      | Simon Zoller             | 24            |
| VfL Bochum      | Paul Grave               | 23            |
| VfL Bochum      | Silvère Ganvoula         | 22            |
| VfL Bochum      | Cristian Gamboa          | 21            |
| VfL Bochum      | Vasilios Lampropoulos    | 20            |
| VfL Bochum      | Gerrit Holtmann          | 19            |
| VfL Bochum      | Erhan Masovic            | 18            |
| VfL Bochum      | Takuma Asano             | 14            |
| VfL Bochum      | Patrick Osterhage        | 14            |
| VfL Bochum      | Michael Esser            | 14            |
| VfL Bochum      | Christopher Antwi-Adjei  | 14            |
| VfL Bochum      | Jacek Goralski           | 7             |
| VfL Bochum      | Jordi Osei-Tutu          | 7             |
| VfL Bochum      | Kevin Stöger             | 7             |
| VfL Bochum      | Mohammed Tolba           | 7             |
| VfL Bochum      | Philipp Hofmann          | 7             |
| VfL Bochum      | Saidy Janko              | 7             |
| VfL Bochum      | Philipp Förster          | 7             |
| VfL Bochum      | Konstantinos Stafylidis  | 6             |
| VfL Bochum      | Ivan Ordets              | 5             |
| VfL Bochum      | Dominique Heintz         | 4             |
| VfL Bochum      | Marko Johansson          | 3             |
| VfL Bochum      | Keven Schlotterbeck      | 2             |
| VfL Bochum      | Pierre Kunde             | 1             |
| VfL Wolfsburg   | Maximilian Arnold        | 24            |
| VfL Wolfsburg   | Koen Casteels            | 23            |
| VfL Wolfsburg   | Yannick Gerhardt         | 21            |
| VfL Wolfsburg   | Josuha Guilavogui        | 21            |
| VfL Wolfsburg   | Pavao Pervan             | 20            |
| VfL Wolfsburg   | Paulo Otávio             | 18            |
| VfL Wolfsburg   | Niklas Klinger           | 18            |
| VfL Wolfsburg   | Omar Marmoush            | 17            |
| VfL Wolfsburg   | Maxence Lacroix          | 16            |
| VfL Wolfsburg   | Ridle Baku               | 15            |
| VfL Wolfsburg   | Philipp Schulze          | 14            |
| VfL Wolfsburg   | Sebastiaan Bornauw       | 12            |
| VfL Wolfsburg   | Lukas Nmecha             | 12            |
| VfL Wolfsburg   | Felix Nmecha             | 11            |
| VfL Wolfsburg   | Luca Waldschmidt         | 10            |
| VfL Wolfsburg   | Micky van de Ven         | 9             |
| VfL Wolfsburg   | Kevin Paredes            | 8             |
| VfL Wolfsburg   | Jonas Wind               | 7             |
| VfL Wolfsburg   | Patrick Wimmer           | 3             |
| VfL Wolfsburg   | Kilian Fischer           | 3             |
| VfL Wolfsburg   | Jakub Kaminski           | 3             |
| VfL Wolfsburg   | Bartol Franjic           | 3             |
| VfL Wolfsburg   | Dzenan Pejcinovic        | 2             |
| VfL Wolfsburg   | Mattias Svanberg         | 1             |
| Werder Bremen   | Michael Zetterer         | 23            |
| Werder Bremen   | Milos Veljkovic          | 22            |
| Werder Bremen   | Jiri Pavlenka            | 20            |
| Werder Bremen   | Niklas Schmidt           | 20            |
| Werder Bremen   | Jean Manuel Mbom         | 19            |
| Werder Bremen   | Romano Schmid            | 18            |
| Werder Bremen   | Niclas Füllkrug          | 15            |
| Werder Bremen   | Ilia Gruev               | 15            |
| Werder Bremen   | Marco Friedl             | 15            |
| Werder Bremen   | Felix Agu                | 13            |
| Werder Bremen   | Dudu                     | 13            |
| Werder Bremen   | Leonardo Bittencourt     | 12            |
| Werder Bremen   | Christian Groß           | 11            |
| Werder Bremen   | Eren Dinkci              | 10            |
| Werder Bremen   | Anthony Jung             | 9             |
| Werder Bremen   | Marvin Ducksch           | 8             |
| Werder Bremen   | Fabio Chiarodia          | 7             |
| Werder Bremen   | Amos Pieper              | 3             |
| Werder Bremen   | Niklas Stark             | 3             |
| Werder Bremen   | Jens Stage               | 3             |
| Werder Bremen   | Dikeni Salifou           | 3             |
| Werder Bremen   | Lee Buchanan             | 2             |
| Werder Bremen   | Mitchell Weiser          | 1             |
```
### 3) Клуби, де середня вартість французьких гравців більша за 5 млн
```sql
SELECT
    club
    , AVG(price) AS avg_french_price
FROM bundesliga_player
WHERE nationality LIKE 'France%'
GROUP BY club
HAVING avg_french_price > 5
ORDER BY avg_french_price DESC;

Результат:

| club            | avg_french_price   |
|-----------------|--------------------|
| RB Leipzig      | 54                 |
| Bayern Munich   | 46                 |
| E. Frankfurt    | 34.833333333333336 |
| B. Leverkusen   | 32.5               |
| Bor. M'gladbach | 15.419999999999998 |
| VfL Wolfsburg   | 9.333333333333334  |
| 1.FSV Mainz 05  | 8.75               |
| TSG Hoffenheim  | 8.5                |
| SC Freiburg     | 6.6                |
| Hertha BSC      | 6.1                |
```
### 4) Клуби, де частка німців вища за 90%
```sql
SELECT
    club
    , COUNTIf(nationality LIKE 'Germany%') / COUNT() AS german_ratio
FROM bundesliga_player
GROUP BY club
HAVING german_ratio > 0.9;

Результат:

| club            | german_ratio |
|-----------------|--------------|
| RB Leipzig U19  | 1            |
| Hertha BSC U19  | 1            |
| W. Bremen U19   | 1            |
| FC Augsburg U19 | 1            |
| W. Bremen II    | 1            |
| 1.FC Köln U19   | 1            |
| RB Leipzig U17  | 1            |
| Hertha BSC II   | 1            |
```
### 5) Найдорожчий у своєму віці гравець (на виході ім'я + вартість)
```sql
SELECT 
    age
    , name AS player_name
    , price AS max_price
FROM (
    SELECT
        age
        , name
        , price
        , ROW_NUMBER() OVER (PARTITION BY age ORDER BY price DESC) AS rn
    FROM bundesliga_player
) t
WHERE rn = 1
ORDER BY age;

Результат:

| age | player_name         | max_price |
|-----|---------------------|-----------|
| 17  | Julien Duranville   | 5         |
| 18  | Youssoufa Moukoko   | 30        |
| 19  | Jude Bellingham     | 120       |
| 20  | Jamal Musiala       | 110       |
| 21  | Josko Gvardiol      | 75        |
| 22  | Alphonso Davies     | 70        |
| 23  | Matthijs de Ligt    | 75        |
| 24  | Randal Kolo Muani   | 65        |
| 25  | Christopher Nkunku  | 80        |
| 26  | Kingsley Coman      | 65        |
| 27  | Leroy Sané          | 70        |
| 28  | Joshua Kimmich      | 80        |
| 29  | Matthias Ginter     | 20        |
| 30  | Mario Götze         | 13        |
| 31  | Sadio Mané          | 45        |
| 32  | Kevin Trapp         | 8.5       |
| 33  | Thomas Müller       | 18        |
| 34  | Mats Hummels        | 6.5       |
| 35  | Rafal Gikiewicz     | 1.2       |
| 36  | Christopher Trimmel | 0.8       |
| 37  | Manuel Neuer        | 7         |
| 38  | Philipp Pentke      | 0.3       |
| 39  | Makoto Hasebe       | 0.8       |
```
### 6) Гравці з вартістю у 1.5 рази більше ніж у середньому за своєю позицією
```sql
SELECT
    bp.name
    , bp.club
    , bp.position
    , bp.price
    , pos_avg.avg_price
FROM bundesliga_player AS bp
JOIN (
    SELECT 
        position
        , AVG(price) AS avg_price
    FROM bundesliga_player
    GROUP BY position
) AS pos_avg
ON bp.position = pos_avg.position
WHERE bp.price > 1.5 * pos_avg.avg_price
ORDER BY price DESC;

Результат:

| name                  | club            | position                      | price | avg_price          |
|-----------------------|-----------------|-------------------------------|-------|--------------------|
| Jude Bellingham       | Bor. Dortmund   | midfield - Central Midfield   | 120   | 10.083928571428574 |
| Jamal Musiala         | Bayern Munich   | midfield - Attacking Midfield | 110   | 13.0725            |
| Florian Wirtz         | B. Leverkusen   | midfield - Attacking Midfield | 85    | 13.0725            |
| Joshua Kimmich        | Bayern Munich   | midfield - Defensive Midfield | 80    | 6.995              |
| Christopher Nkunku    | RB Leipzig      | Attack - Second Striker       | 80    | 17.928571428571427 |
| Matthijs de Ligt      | Bayern Munich   | Defender - Centre-Back        | 75    | 10.206818181818184 |
| Josko Gvardiol        | RB Leipzig      | Defender - Centre-Back        | 75    | 10.206818181818184 |
| Leroy Sané            | Bayern Munich   | Attack - Right Winger         | 70    | 10.865000000000002 |
| Alphonso Davies       | Bayern Munich   | Defender - Left-Back          | 70    | 6.977500000000001  |
| Kingsley Coman        | Bayern Munich   | Attack - Left Winger          | 65    | 12.218181818181819 |
| Leon Goretzka         | Bayern Munich   | midfield - Central Midfield   | 65    | 10.083928571428574 |
| Randal Kolo Muani     | E. Frankfurt    | Attack - Centre-Forward       | 65    | 7.571232876712331  |
| João Cancelo          | Bayern Munich   | Defender - Right-Back         | 60    | 7.721590909090909  |
| Dayot Upamecano       | Bayern Munich   | Defender - Centre-Back        | 60    | 10.206818181818184 |
| Serge Gnabry          | Bayern Munich   | Attack - Right Winger         | 55    | 10.865000000000002 |
| Moussa Diaby          | B. Leverkusen   | Attack - Right Winger         | 50    | 10.865000000000002 |
| Lucas Hernández       | Bayern Munich   | Defender - Centre-Back        | 50    | 10.206818181818184 |
| Sadio Mané            | Bayern Munich   | Attack - Left Winger          | 45    | 12.218181818181819 |
| Nico Schlotterbeck    | Bor. Dortmund   | Defender - Centre-Back        | 40    | 10.206818181818184 |
| Dominik Szoboszlai    | RB Leipzig      | midfield - Attacking Midfield | 40    | 13.0725            |
| Dani Olmo             | RB Leipzig      | midfield - Attacking Midfield | 40    | 13.0725            |
| Julian Brandt         | Bor. Dortmund   | midfield - Attacking Midfield | 40    | 13.0725            |
| Benjamin Pavard       | Bayern Munich   | Defender - Right-Back         | 35    | 7.721590909090909  |
| Jeremie Frimpong      | B. Leverkusen   | Defender - Right-Back         | 35    | 7.721590909090909  |
| Gregor Kobel          | Bor. Dortmund   | Goalkeeper                    | 35    | 2.085869565217392  |
| Niklas Süle           | Bor. Dortmund   | Defender - Centre-Back        | 35    | 10.206818181818184 |
| Karim Adeyemi         | Bor. Dortmund   | Attack - Left Winger          | 35    | 12.218181818181819 |
| Evan Ndicka           | E. Frankfurt    | Defender - Centre-Back        | 32    | 10.206818181818184 |
| Marcus Thuram         | Bor. M'gladbach | Attack - Centre-Forward       | 32    | 7.571232876712331  |
| Manu Koné             | Bor. M'gladbach | midfield - Central Midfield   | 30    | 10.083928571428574 |
| Edmond Tapsoba        | B. Leverkusen   | Defender - Centre-Back        | 30    | 10.206818181818184 |
| Sébastien Haller      | Bor. Dortmund   | Attack - Centre-Forward       | 30    | 7.571232876712331  |
| Daichi Kamada         | E. Frankfurt    | midfield - Attacking Midfield | 30    | 13.0725            |
| Patrik Schick         | B. Leverkusen   | Attack - Centre-Forward       | 30    | 7.571232876712331  |
| Ryan Gravenberch      | Bayern Munich   | midfield - Central Midfield   | 30    | 10.083928571428574 |
| Youssoufa Moukoko     | Bor. Dortmund   | Attack - Centre-Forward       | 30    | 7.571232876712331  |
| Jesper Lindström      | E. Frankfurt    | midfield - Attacking Midfield | 28    | 13.0725            |
| Mohamed Simakan       | RB Leipzig      | Defender - Centre-Back        | 28    | 10.206818181818184 |
| Giovanni Reyna        | Bor. Dortmund   | midfield - Attacking Midfield | 28    | 13.0725            |
| Konrad Laimer         | RB Leipzig      | midfield - Central Midfield   | 28    | 10.083928571428574 |
| Noussair Mazraoui     | Bayern Munich   | Defender - Right-Back         | 28    | 7.721590909090909  |
| Timo Werner           | RB Leipzig      | Attack - Centre-Forward       | 25    | 7.571232876712331  |
| Piero Hincapié        | B. Leverkusen   | Defender - Centre-Back        | 25    | 10.206818181818184 |
| Djibril Sow           | E. Frankfurt    | midfield - Central Midfield   | 22    | 10.083928571428574 |
| Xaver Schlager        | RB Leipzig      | midfield - Central Midfield   | 22    | 10.083928571428574 |
| Matthias Ginter       | SC Freiburg     | Defender - Centre-Back        | 20    | 10.206818181818184 |
| Christoph Baumgartner | TSG Hoffenheim  | midfield - Attacking Midfield | 20    | 13.0725            |
| Jonathan Tah          | B. Leverkusen   | Defender - Centre-Back        | 20    | 10.206818181818184 |
| Odilon Kossounou      | B. Leverkusen   | Defender - Centre-Back        | 20    | 10.206818181818184 |
| Mathys Tel            | Bayern Munich   | Attack - Centre-Forward       | 20    | 7.571232876712331  |
| Raphaël Guerreiro     | Bor. Dortmund   | Defender - Left-Back          | 20    | 6.977500000000001  |
| Maxence Lacroix       | VfL Wolfsburg   | Defender - Centre-Back        | 20    | 10.206818181818184 |
| Ramy Bensebaini       | Bor. M'gladbach | Defender - Left-Back          | 20    | 6.977500000000001  |
| Nico Elvedi           | Bor. M'gladbach | Defender - Centre-Back        | 20    | 10.206818181818184 |
| Philipp Lienhart      | SC Freiburg     | Defender - Centre-Back        | 20    | 10.206818181818184 |
| André Silva           | RB Leipzig      | Attack - Centre-Forward       | 20    | 7.571232876712331  |
| David Raum            | RB Leipzig      | Defender - Left-Back          | 20    | 6.977500000000001  |
| Micky van de Ven      | VfL Wolfsburg   | Defender - Centre-Back        | 18    | 10.206818181818184 |
| Salih Özcan           | Bor. Dortmund   | midfield - Defensive Midfield | 17    | 6.995              |
| Amadou Haidara        | RB Leipzig      | midfield - Central Midfield   | 17    | 10.083928571428574 |
| Maximilian Arnold     | VfL Wolfsburg   | midfield - Central Midfield   | 17    | 10.083928571428574 |
| Benjamin Henrichs     | RB Leipzig      | Defender - Right-Back         | 15    | 7.721590909090909  |
| Anton Stach           | 1.FSV Mainz 05  | midfield - Defensive Midfield | 15    | 6.995              |
| Adam Hlozek           | B. Leverkusen   | Attack - Centre-Forward       | 15    | 7.571232876712331  |
| Borna Sosa            | VfB Stuttgart   | Defender - Left-Back          | 15    | 6.977500000000001  |
| Julian Weigl          | Bor. M'gladbach | midfield - Defensive Midfield | 15    | 6.995              |
| Lukas Nmecha          | VfL Wolfsburg   | Attack - Centre-Forward       | 14    | 7.571232876712331  |
| Jonas Wind            | VfL Wolfsburg   | Attack - Centre-Forward       | 14    | 7.571232876712331  |
| Emre Can              | Bor. Dortmund   | midfield - Defensive Midfield | 14    | 6.995              |
| Angeliño              | TSG Hoffenheim  | Defender - Left-Back          | 14    | 6.977500000000001  |
| Niclas Füllkrug       | Werder Bremen   | Attack - Centre-Forward       | 13    | 7.571232876712331  |
| Ridle Baku            | VfL Wolfsburg   | Defender - Right-Back         | 13    | 7.721590909090909  |
| Ellyes Skhiri         | 1.FC Köln       | midfield - Defensive Midfield | 13    | 6.995              |
| Ermedin Demirovic     | FC Augsburg     | Attack - Centre-Forward       | 12    | 7.571232876712331  |
| Joe Scally            | Bor. M'gladbach | Defender - Right-Back         | 12    | 7.721590909090909  |
| Rafael Borré          | E. Frankfurt    | Attack - Centre-Forward       | 12    | 7.571232876712331  |
| Jonathan Burkardt     | 1.FSV Mainz 05  | Attack - Centre-Forward       | 12    | 7.571232876712331  |
| Sardar Azmoun         | B. Leverkusen   | Attack - Centre-Forward       | 12    | 7.571232876712331  |
| Kiliann Sildillia     | SC Freiburg     | Defender - Right-Back         | 12    | 7.721590909090909  |
| Josip Stanisic        | Bayern Munich   | Defender - Right-Back         | 12    | 7.721590909090909  |
| Lukas Klostermann     | RB Leipzig      | Defender - Right-Back         | 12    | 7.721590909090909  |
| Mergim Berisha        | FC Augsburg     | Attack - Centre-Forward       | 12    | 7.571232876712331  |
| Lucas Tousart         | Hertha BSC      | midfield - Defensive Midfield | 11    | 6.995              |
| Robert Andrich        | B. Leverkusen   | midfield - Defensive Midfield | 11    | 6.995              |
| Robert Skov           | TSG Hoffenheim  | midfield - Right Midfield     | 9     | 4.525              |
| Kevin Trapp           | E. Frankfurt    | Goalkeeper                    | 8.5   | 2.085869565217392  |
| Koen Casteels         | VfL Wolfsburg   | Goalkeeper                    | 8     | 2.085869565217392  |
| Junior Dina Ebimbe    | E. Frankfurt    | midfield - Right Midfield     | 7.5   | 4.525              |
| Mark Flekken          | SC Freiburg     | Goalkeeper                    | 7     | 2.085869565217392  |
| Jonas Omlin           | Bor. M'gladbach | Goalkeeper                    | 7     | 2.085869565217392  |
| Manuel Neuer          | Bayern Munich   | Goalkeeper                    | 7     | 2.085869565217392  |
| Péter Gulácsi         | RB Leipzig      | Goalkeeper                    | 6     | 2.085869565217392  |
| Robin Zentner         | 1.FSV Mainz 05  | Goalkeeper                    | 5     | 2.085869565217392  |
| Yann Sommer           | Bayern Munich   | Goalkeeper                    | 5     | 2.085869565217392  |
| Marvin Schwäbe        | 1.FC Köln       | Goalkeeper                    | 5     | 2.085869565217392  |
| Oliver Baumann        | TSG Hoffenheim  | Goalkeeper                    | 4.5   | 2.085869565217392  |
| Oliver Christensen    | Hertha BSC      | Goalkeeper                    | 4.5   | 2.085869565217392  |
| Frederik Rönnow       | Union Berlin    | Goalkeeper                    | 3.5   | 2.085869565217392  |
```

### 7) На якій позиції найважче отримати контракт з будь-якою компанією 
(припущення: виходжу з того, що гравцям без агента складніше самостійно отримати контракт)
```sql
SELECT 
    position
    , SUM(if(player_agent = '', 1, 0)) / COUNT() AS no_agent_ratio
FROM bundesliga_player
GROUP BY position
ORDER BY no_agent_ratio DESC
LIMIT 1;

Результат:
| position                 | no_agent_ratio |
|--------------------------|----------------|
| midfield - Left Midfield | 0.5            |
```

### 8) У якій команді найперше закінчиться контракт у 5 гравців
```sql
SELECT
    club
    ,fifth_earliest_expiry
FROM (
    SELECT
        club
        , contract_expires AS fifth_earliest_expiry
        , row_number() OVER (PARTITION BY club ORDER BY toDate(contract_expires)) AS rn
    FROM bundesliga_player
)
WHERE rn = 5
ORDER BY toDate(fifth_earliest_expiry) ASC
LIMIT 1;

Результат:
| club         | fifth_earliest_expiry |
|--------------|-----------------------|
| Union Berlin | 1970-01-01            |
```

### 9) У якому віці гравці здебільшого виходять на пік своєї вартості
```sql
SELECT
    age
    , AVG(max_price) AS avg_max_price
FROM bundesliga_player
GROUP BY age
ORDER BY avg_max_price DESC
LIMIT 1;

Результат:

| age | avg_max_price      |
|-----|--------------------|
| 27  | 26.094117647058823 |
```

### 10) У якої команди найзіграніший склад (найдовше грають разом)
```sql
SELECT
    club
    , AVG(dateDiff('day', toDate(joined_club), today())) AS avg_years_in_club
FROM bundesliga_player
GROUP BY club
ORDER BY avg_years_in_club DESC
LIMIT 1;

Результат:

| club            | avg_years_in_club |
|-----------------|-------------------|
| Bor. M'gladbach | 2375.740740740741 |
```

### 11) У яких командах є тезки
```sql
SELECT 
    club,
    name,
    COUNT() AS count_same_name
FROM bundesliga_player
GROUP BY club, name
HAVING count_same_name > 1
ORDER BY club, count_same_name DESC;


Результат:

No records found
```

### 12) Команди, де топ-3 гравці займають 50% платіжної відомості
```sql
SELECT 
    club
FROM (
    SELECT club
        , name
        , price
        , SUM(price) OVER (PARTITION BY club ORDER BY price DESC) AS cumulative_price
        , SUM(price) OVER (PARTITION BY club) AS total_club_price
        , row_number() OVER (PARTITION BY club ORDER BY price DESC) AS rn
    FROM bundesliga_player
) 
WHERE rn <= 3 AND cumulative_price / total_club_price >= 0.5
GROUP BY club;

Результат:

| club           |
|----------------|
| RB Leipzig U19 |
| B. Dortmund II |
| Hertha BSC U19 |
| W. Bremen U19  |
| W. Bremen II   |
| 1.FC Köln II   |
| RB Leipzig U17 |
| Hertha BSC II  |
```
