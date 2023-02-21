`` Università degli Studi di Milano `` `` Corso di Editoria Digitale`` 
`` Anno Accademico 2022/2023`` `` Mirco Soldani 954698`` 

# Report e analisi degli ascolti mensili di un utente utilizzando le API di Last.fm e Spotify



## Introduzione

Last.fm è un social network che permette di tener traccia dei propri ascolti su Spotify/Apple Music/Youtube Music. Il sistema costruisce un profilo per ogni utente e include i suoi artisti e canzoni preferite in un sub-sito personalizzabile. Le canzoni  ascoltate vi compaiono dopo averle ascoltate con l'apposita radio oppure tramite un plugin installato nel proprio player.[^1]

Lo scopo del mio progetto sarà la creazione di un report mensile con informazioni riguardo le canzoni, gli album e le tracce più ascoltate dall'utente con insight sulle caratteristiche delle tracce ascoltate dall'utente (forniti dalle API Spotify)

XML, XSLT e EPUB hanno permesso la realizzazione di questo progetto, essendo in grado di trasformare contenuto non fruibile da tutti gli utenti, in una esperienza gradevole sotto tutti i punti di vista.

## Obiettivi 

L'obiettivo principale è incuriosire gli utenti (interessati a statistiche sui propri ascolti e non) ad una analisi più profonda basata su dati generati dai propri ascolti e abitudini di ascolto in generale. Questo progetto è destinato anche a persone in cerca di ispirazione, se, ad esempio, sono in cerca di nuovi artisti da ascoltare. 

E' possibile, quindi, definire il progetto come una estensione della funzione Spotify Wrapped a ogni mese dell'anno, con l'aggiunta di informazioni mirate e specifiche fornite da Last.fm.

## Processo di produzione

### Studio e analisi tema

- **Identificazione delle fonti:** la piattaforma Last.fm fornisce già all'utente registrato informazioni e statistiche inerenti a tracce, album e artisti più ascoltati. E' possibile estrarre queste informazioni tramite le apposite API. Le percentuali sulle caratteristiche delle tracce sono state estratte grazie alle API di Spotify.
- **Definizione del target**: il target è composto dalle persone che fanno uso di Last.fm, senza di esso non si avrebbero le informazioni necessarie per una descrizione accurata degli ascolti, ma solo quelle generiche che forniscono le API di Spotify. Se un utente fa uso di Last.fm, è indifferente aver collegato ad esso un account Spotify/Apple Music/Youtube Music.
- **Studio competitor/tools già esistenti:** le applicazioni online tendenzialmente gestiscono informazioni esclusivamente di una delle due piattaforme, mai delle due contemporaneamente. I dati derivati dalle API di entrambe le piattaforme possono essere manipolate in infiniti modi.
- **Diritti legali**: i dati degli utenti registrati su Last.fm sono tendenzialmente pubblici, è possibile, quindi, accedere ad essi in ogni momento. I dati di Spotify, invece, non sono legati in nessun modo all'utente.

### Raccolta dei contenuti

- **Permessi richiesti:** per utilizzare le informazioni delle piattaforme e fare uso delle API, è stato necessario richiedere l'autorizzazione, ottenendo una chiave API. Per quanto riguarda Spotify, ogni x minuti bisognava richiedere un OAuth Token [^2] per effettuare le chiamate API.

Le API utilizzate in questo progetto, come già detto, sono di piattaforme diverse e, per questo motivo, verranno analizzate separatamente.

**Last.fm**[^3]

- [user.getTopAlbums](https://www.last.fm/api/show/user.getTopAlbums)

  - Output: XML con gli album più ascoltati da un determinato user.

  - Esempio chiamata URL: /2.0/method=user.gettopalbums&user=puremelodrama&api_key=YOUR_API_KEY&limit=10&period=1month

    - Il periodo, essendo un report mensile, viene settato a "1month" usando il parametro opzionale period

    - In questo caso ritenevo opportuno limitare gli album più ascoltati in output a 10.

      ```xml
      <topalbums user="RJ" type="overall">
        <album rank="1">
          <name>Images and Words</name>
          <playcount>174</playcount>
          <mbid>f20971f2-c8ad-4d26-91ab-730f6dedafb2</mbid>  
          <url>
            http://www.last.fm/music/Dream+Theater/Images+and+Words
          </url>
          <artist>
            <name>Dream Theater</name>
            <mbid>28503ab7-8bf2-4666-a7bd-2644bfc7cb1d</mbid>
            <url>http://www.last.fm/music/Dream+Theater</url>
          </artist>
          <image size="small">...</image>
          <image size="medium">...</image>
          <image size="large">...</image>
        </album>
      </topalbums>
      
      ```

      

- [user.getTopArtists](https://www.last.fm/api/show/user.getTopArtists)

  - Output: XML con gli artisti più ascoltati dall'utente.

  - Esempio chiamata URL: /2.0/method=user.gettopartists&user=puremelodrama&api_key=YOUR_API_KEY&limit=10&period=1month

    - Periodo impostato a 1 mese

    - Limite impostato a 10

      ```xml
      <topartists user="RJ" type="overall">
        <artist rank="1">
          <name>Dream Theater</name>
          <playcount>1337</playcount>
          <mbid>28503ab7-8bf2-4666-a7bd-2644bfc7cb1d</mbid>
          <url>http://www.last.fm/music/Dream+Theater</url>
          <streamable>1</streamable>
          <image size="small">...</image>
          <image size="medium">...</image>
          <image size="large">...</image>
        </artist>
        ...
      </topartists>
      
      ```

      

- [user.getTopTracks](https://www.last.fm/api/show/user.getTopTracks)

  - Output: XML con le tracce più ascoltate dall'utente.

  - Esempio chiamata URL: /2.0/method=user.gettoptracks&user=rj&api_key=YOUR_API_KEY&limit=10&period=1month

    - Periodo impostato a 1 mese

    - Limite impostato a 10

      ```xml
      <toptracks user="RJ" type="overall">  
        <track rank="1">
          <name>Learning to Live</name>
          <playcount>42</playcount>
          <mbid/>
          <url>
            http://www.last.fm/music/Dream+Theater/_/Learning+to+Live
          </url>
          <streamable fulltrack="0">1</streamable>
          <artist>
            <name>Dream Theater</name>
            <mbid>28503ab7-8bf2-4666-a7bd-2644bfc7cb1d</mbid>
            <url>http://www.last.fm/music/Dream+Theater</url>
          </artist>
          <image size="small">...</image>
          <image size="medium">...</image>
          <image size="large">...</image>
        </track>
        ...
      </toptracks>
      
      ```

- [artist.getSimilar](https://www.last.fm/api/show/artist.getSimilar)

  - Output: XML con informazioni su artisti simili all'artista più ascoltato dall'utente.

  - Esempio chiamata URL: /2.0/method=artist.getsimilar&artist=cher&api_key=YOUR_API_KEY 

    - Artista specificato: top artist dell'utente

    ```xml
    <similarartists artist="Cher">
      <artist>
        <name>Sonny & Cher</name>
        <mbid>3d6e4b6d-2700-458c-9722-9021965a8164</mbid>
        <match>1</match>
        <url>www.last.fm/music/Sonny%2B%2526%2BCher</url>
        <image size="small">http://userserve-ak.last.fm/serve/34/71168880.png</image>
        <image size="medium">http://userserve-ak.last.fm/serve/64/71168880.png</image>
        <image size="large">http://userserve-ak.last.fm/serve/126/71168880.png</image>
        <image size="extralarge">http://userserve-ak.last.fm/serve/252/71168880.png</image>
        <image size="mega">http://userserve-ak.last.fm/serve/500/71168880/Sonny++Cher.png</image>
        <streamable>1</streamable>
      </artist>
      ...
    </similarartists>
    ```

#### Spotify[^4]

Una volta ottenute le canzoni più ascoltate dall'utente, sono stati estratti gli ID delle canzoni in top 10 dai link delle stesse su Spotify. In questo modo è stato possibile effettuare la chiamata alla API della piattaforma. Le informazioni sono state ottenute tramite l'uso della console presente sul sito di Spotify for Developers [^5].

- [**Get Track's Audio Features**](https://developer.spotify.com/documentation/web-api/reference/#/operations/get-audio-features)
  - Tramite l'ID della canzone è possibile risalire alle informazioni inerenti danceability, energy, acousticness, valence e speechiness.
    - La <u>danceability</u> (ballabilità) descrive quanto un brano sia adatto al ballo in base a una combinazione di elementi musicali, tra cui il tempo, la stabilità del ritmo, la forza del battito e la regolarità generale.
    - L'<u>energia</u> rappresenta una misura percettiva dell'intensità e dell'attività. In genere, i brani energetici sono veloci, forti e rumorosi. Le caratteristiche percettive che contribuiscono a questo attributo includono la gamma dinamica, il volume percepito, il timbro, la velocità di insorgenza e l'entropia generale.
    -  I brani con alta <u>valenza</u> suonano più positivi (ad esempio, felici, allegri, euforici),  mentre quelli con bassa valenza suonano più negativi (ad esempio, tristi, depressi, arrabbiati).
    - <u>Speechiness</u> rileva la presenza di parole parlate in una traccia. Più la registrazione è esclusivamente di tipo parlato (ad esempio, talk show, audiolibri, poesie), più il valore dell'attributo è vicino al 100%.
  - I dati in output, essendo in formato JSON sono stati trasformati in formato XML e inclusi nel file contenente le informazioni già ricavate.
- **[Get Tracks' Audio Features](https://developer.spotify.com/console/get-audio-features-several-tracks/)**
  - Funziona esattamente come la funzione sopra ma prende più ID di canzoni alla volta.

### Revisione dei contenuti

- **Identificazioni di elementi multimediali:** artisti e album ottenuti dalle chiamate alle API sono stati resi link ipertestuali per creare collegamenti con quella che è la piattaforma di Last.fm.
- **Definizione dello stile grafico**: semplicità e continuità sono le caratteristiche del progetto.
- **Coerenza tra i formati:** per rispettare l'esperienza di fruizione dei contenuti, è stato necessario riadattare alcuni elementi da una trasformazione all'altra.
- **Verifica post-produzione:**  verifica compatibilità con dispositivi diversi, verifica del target e revisione dei contenuti.

## Gestione documentale

![diagram(1)](C:\Users\mirco\Downloads\diagram(3).svg)



## Tecnologie adottate

Ogni chiamata alle API di Last.fm produce informazioni in formato XML. Tutte le informazioni utili vengono riunite in un file unico .xml.

Il progetto produce il report mensile su 2 formati diversi:

- HTML
- EPUB

Per produrre l'HTML è stato fatto uso di XSLT.

Per mostrare all'utente la top 10 delle sue canzoni più ascoltate e i suoi album più ascoltati, verranno generate due tabelle. 

Esempio:

```xml
<div style = "margin-top: 30px">
    <font size="6" face="arial" color="#008000"><center>Gli album che ti sono piaciuti di più:</center></font><br></br>
    <table style = "text-align:center; font-size:120%; face:arial; margin-left:auto; margin-right:auto;">
      <tr bgcolor="#9acd32">
        <th style="text-align:center"></th>
        <th style="text-align:center"></th>
        <th style="text-align:center">Artist</th>
        <th style="text-align:center">Album</th>
        <th style="text-align:center">Playcount</th>
      </tr>
      <xsl:for-each select="topalbums/album">
      <tr>
        <td><xsl:value-of select="@rank"/></td>
        <td><img>
              <xsl:attribute name="src">
                <xsl:value-of select="image"/>
              </xsl:attribute>
            </img></td>
        <td><xsl:value-of select="artist/name"/></td>
        <td>
        <xsl:element name="a">
              <xsl:attribute name="href">
                  <xsl:value-of select="url"/>
              </xsl:attribute>
              <xsl:attribute name="target">_blank</xsl:attribute>
              <xsl:value-of select="name"/>
        </xsl:element>
        </td>
        <td><xsl:value-of select="playcount"/></td>
      </tr>
      </xsl:for-each>
    </table>
  </div>
```

Per quanto riguarda invece le informazioni sugli artisti più ascoltati, verrà visualizzata una lista che metterà in risalto gli artisti più ascoltati mettendoli in ordine crescente.

```xml
<div style = "margin-top: 30px">
    <br></br><font size="7" face="arial" color="#008000"><center>Hai ascoltato parecchi artisti, <br/> ma i 10 che ti sono piaciuti di più sono:</center></font><br></br>
    <xsl:for-each select="topartists/artist">
      <xsl:if test="(@rank = 1) or (@rank = 2) or (@rank=3)">
        <font size="6" face="arial" color="#ff0000"><center><xsl:value-of select="name"/></center></font>
        <font size="5" face="arial" color="#000000"><center><xsl:value-of select="playcount"/> scrobbles</center></font><br></br>
      </xsl:if>
      <xsl:if test="(@rank = 4) or (@rank = 5) or (@rank=6)">
        <font size="6" face="arial" color="#ff6a00"><center><xsl:value-of select="name"/></center></font>
        <font size="5" face="arial" color="#000000"><center><xsl:value-of select="playcount"/> scrobbles</center></font><br></br>
      </xsl:if>
      <xsl:if test="(@rank = 7) or (@rank = 8) or (@rank=9) or (@rank=10)">
        <font size="6" face="arial" color="#ffa600"><center><xsl:value-of select="name"/></center></font>
        <font size="5" face="arial" color="#000000"><center><xsl:value-of select="playcount"/> scrobbles</center></font><br></br>
      </xsl:if>
    </xsl:for-each>
  </div>
```

Le informazioni restituite dalle chiamate alle API di Spotify, invece, essendo in formato JSON devono essere convertite in XML. Per fare ciò è possibile fare uso di librerie Python specifiche, ma essendo dati semplici, è bastato un [convertitore online.](https://www.convertjson.com/json-to-xml.htm) 

#### Generazione del report in formato .html:

Ho fatto uso della libreria di Python lxml, con il seguente script:

```
from lxml import etree
load input
dom = etree.parse('editoria.xml')
transform = etree.XSLT(etree.parse('editoria.xsl'))

apply XSLT on loaded dom

s = str(transform(dom))
print(s)
```

Il codice ottenuto viene salvato in un file .xhtml.

#### Generazione del report in formato .epub:

Per convertire il file .xhtml in EPUB ho usato di [Calibre](https://calibre-ebook.com/), strumento utilissimo sia nella conversione che nella lettura di EPUB e altri formati. 

Una volta ottenuto l'XHTML è possibile convertirlo facilmente dall'interfaccia di Calibre,  aggiungendo per prima cosa il file alla libreria. L'interfaccia permette di selezionare il formato in output (in questo caso l'output sarà un .epub) ma permette anche di inserire metadati e informazioni aggiuntive. Nella sezione "Indice" 

![image-20230220163108356](C:\Users\mirco\Desktop\Editoria\Progetto\image-20230220163108356.png)

Dopo aver aggiunto metadati quali titolo e autore, è possibile effettivamente iniziare a modificare il contenuto dell'EPUB. 

Un EPUB ha una struttura definita: è composto da diverse sezioni, tra cui testo, stili, immagini, caratteri e miscellaneous. 

Subito dopo aver convertito il file .xhtml nella sezione "Testo" era presente un solo file contenente l'intero progetto. Per suddividere meglio le sezioni e creare un percorso di fruizione lineare per l'utente, il file è stato diviso in file separati. 

Ogni file è stato successivamente aggiunto all'indice, contenuto nella sezione "Miscellaneous" (toc.ncx).

Nella sezione "Stili", sono presenti CSS che vanno a definire lo stile delle pagine e dei singoli elementi che compongono l'EPUB.



<img src="C:\Users\mirco\AppData\Roaming\Typora\typora-user-images\image-20230220164141578.png" alt="image-20230220164141578" style="zoom: 80%;" />



La cover dell'EPUB è stata generata tramite un web app che fa un collage degli album più ascoltati dell'utente inserito tramite le API di Last.fm.

<img src="C:\Users\mirco\Desktop\Editoria\Progetto\collage.jpeg" alt="collage" style="zoom:50%;" />



Pandoc poteva essere utilizzato per trasformare l'HTML in EPUB e altri formati, ma volendo più libertà nella personalizzazione ho optato per l'opzione di usare calibre.

## Conclusioni

I file XHTML e EPUB ottenuti rispecchiano esattamente gli obiettivi prefissati. Il processo di automazione per ottenere questi risultati può essere ottimizzato creando effettivamente una web app da collegare alle API di Last.fm e Spotify. Ne risente soprattutto l'estrazione manuale delle singole canzoni dai link di Spotify. Trovare una corrispondenza biunivoca tra la canzone registrata negli ascolti di Last.fm e quella su Spotify non è sempre facile: Spotify ha più versioni degli stessi album/tracce sulla propria piattaforma. Una possibile limitazione, però, può essere proprio la collaborazione tra le due aziende e la gestione dei diritti d'autore e/o privacy dei dati dell'utente. Alcune funzionalità interessanti delle API di Spotify diventano accessibili solo quando si sta sviluppando una Web app.

Avere dati e numeri specifici dei vari play count di artisti, tracce o album così come percentuali sui propri dati fornisce una sicurezza e credibilità alla piattaforma. Altro punto di forza è proprio il riepilogo mensile *(ultimi 30 giorni)* degli ascolti dell'utente: attualmente le API di Spotify permettono di estrarre informazioni riguardo gli ultimi 7 giorni, 6 mesi o dalla creazione dell'account. Un resoconto mensile è il giusto compromesso tra tutte le opzioni.

## Bibliografia e sitografia

[^1]: https://it.wikipedia.org/wiki/Last.fm
[^2]: https://developer.spotify.com/documentation/general/guides/authorization/
[^3]: https://www.last.fm/api
[^4]: https://developer.spotify.com/documentation/web-api/libraries/
[^5]: https://developer.spotify.com/console/
