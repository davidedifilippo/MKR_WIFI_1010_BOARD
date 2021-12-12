## MKR_WIFI_1010_BOARD

Si include la libreria di gestione del chip WiFi:

        #include <WiFiNINA.h>

Si crea un oggetto WiFiServer in ascolto sulla porta 80: 

        WiFiServer server(80);            //server socket
  
## Setup della scheda

Si cerca di avviare il chip wifi in modalità AP (access Point) provando ogni 10 secondi a connettersi al router locale 
   
        //Stato attuale -> IDLE 
        
        while (status != WL_CONNECTED) {
        status = WiFi.begin("SSID...", "PASSWORD...");
        delay(10000);  // wait 10 seconds for connection:
          }

Bisogna sostituire a:

- SSID  la stringa identificativa del router
- PASSWORD la password del router 

 Si stampa l'indirizzo IP assegnato dal router alla board:
 
        IPAddress ip = WiFi.localIP();
        Serial.print("IP Address: ");
        Serial.println(ip);


Si avvia il server:

        server.begin();

## Loop 

Ad ogni ciclo di loop si controlla se il client è collegato al punto di accesso:

      client = server.available();
Se il client è connesso:

      if (client == true)
      Serial.println("new client");           // print a message out the serial port
      String currentLine = "";                // make a String to hold incoming data from the client
      
Fino a quando il client è presente e ricevo byte stampo i caratteri sul monitor seriale:
   
      while (client.connected()) {            
      if (client.available()) {             // if there's bytes to read from the client,
        char c = client.read();             // read a byte, then
        Serial.write(c); 
        
Se il carattere ricevuto è diverso da '/r' lo aggiungo alla stringa ricevuta finora:

         if (c != '\r') {    // if you got anything else but a carriage return character,
          currentLine += c;      // add it to the end of the currentLine
          
          

La richiesta del client termina con due a capo sulla stessa riga. Nel momento in cui si ricevono i due \n si invia la risposta al client visualizzabile dal browser:

            if (c == '\n') {                    // if the byte is a newline character

          // if the current line is blank, you got two newline characters in a row.
          // that's the end of the client HTTP request, so send a response:
          if (currentLine.length() == 0) {

            //intestazione
            client.println("HTTP/1.1 200 OK");
            client.println("Content-type:text/html");
            client.println();
            //fine intestazione 
            client.print("Click <a href=\"/H\">here</a> turn the LED on<br>");
            client.print("Click <a href=\"/L\">here</a> turn the LED off<br><br>");
            
Se la stringa ricevuta finisce con "GET /H" allora si deve accendere il led:

        if (currentLine.endsWith("GET /H")) {
          digitalWrite(ledPin, HIGH);        
          }

Se la stringa ricevuta finisce con "GET /L" allora si deve spegnere il led:

        if (currentLine.endsWith("GET /L")) {
          digitalWrite(ledPin, LOW);       
        }

Se il client si disconnette chiudo la connessione:

    client.stop();
    Serial.println("client disconnected");

