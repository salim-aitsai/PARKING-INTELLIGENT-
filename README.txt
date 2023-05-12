# PARKING-INTELLIGENT-
programme d'un parking intelligent arduino\HTML où j'ai utilisé un moteur pas à pas et trois capteurs de proximité et un esp32 qui fonctionne avec une application HTML qui permet l'ouverture et fermeture de la porte et affiche la disponibilité des places


le code utilisé : 

#include <WiFi.h>
#include <WebServer.h>
#include <Stepper.h>
int pin_1 = 32;
int pin_2 = 34;
int pin_3 = 35;
int val_1,val_2,val_3;
// Remplacer les informations suivantes avec vos informations WiFi
const char* ssid = "POCO F3";
const char* password = "1234567890";
// Paramètres pour le moteur pas à pas
const int stepsPerRevolution =512;
Stepper myStepper(stepsPerRevolution, 33,26,27,25);
 
// Créer un objet WebServer
WebServer server(80);
void setup() {
  // Initialisation de la connexion série
  Serial.begin(115200);
  
  // Connexion au réseau WiFi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(100);
    Serial.println("Connexion au reseau Wi-Fi...");
    Serial.println ( "" );
    Serial.print ( "Maintenant connecte a " );
    Serial.println ( ssid );
    Serial.print ( "Adresse IP: " );
    Serial.println ( WiFi.localIP() );
  }
  Serial.println("Connecte au reseau Wi-Fi");
  
  // Définir les pins du moteur pas à pas comme sorties
  pinMode(33, OUTPUT);
  pinMode(27, OUTPUT);
  pinMode(26, OUTPUT);
  pinMode(25, OUTPUT);
   pinMode(pin_1,INPUT);
  pinMode(pin_2,INPUT);
  pinMode(pin_3,INPUT); 
  // Définir la vitesse de rotation du moteur pas à pas
  myStepper.setSpeed(10);
  
  // Définir les routes pour le serveur Web
  server.on("/", handleRoot);
  server.on("/open", handleOpen);
  server.on("/close", handleClose);
  server.on("/nombre",buttonPlace);
  // Démarrer le serveur Web
  server.begin();
  Serial.println("Serveur Web demarre");
}
void loop() {
  // Écouter les requêtes du client
  server.handleClient();
  
}
void handleRoot() {
  // Envoyer la page HTML au client
  String html = "<html><head><style>"
               "h1 { color: blue; }"
               "button { background-color: #4CAF50; border: none; color: white; padding: 15px 32px; text-align: center; text-decoration: none; display: inline-block; font-size: 16px; margin: 4px 2px; cursor: pointer; }"
               "</style></head><body>"
               "<h1> pour verifier la disponibilite de notre PARKING cliquer sur ce button :</h1>"
               "<p><a href=\"/nombre\"><button>ICI</button></a></p>"
               "<h1>l'ouverture et fermeture du portail automatiquement </h1>"
               "<p><a href=\"/open\"><button>Ouvrir</button></a></p>"
               "<p><a href=\"/close\"><button>Fermer</button></a></p>"
               "</body></html>";
  server.send(200, "text/html", html);
}
void handleOpen() {
  // Tourner le moteur pas à pas dans le sens horaire pour ouvrir
  myStepper.step(stepsPerRevolution);
  server.send(200, "text/html", "<html><body><h1>Controle du moteur pas a pas</h1><p>Le moteur pas a pas a ete ouvert</p><p><a href=\"/\"><button>Retour</button></a></p></body></html>");
}
void handleClose() {
  // Tourner le moteur pas à pas dans le sens antihoraire pour fermer
  myStepper.step(-stepsPerRevolution);
  server.send(200, "text/html", "<html><body><h1>Controle du moteur pas a pas</h1><p>Le moteur pas a pas a ete ferme</p><p><a href=\"/\"><button>Retour</button></a></p></body></html>");
}
void buttonPlace(){
  val_1=digitalRead(pin_1);
val_2=digitalRead(pin_2);
val_3=digitalRead(pin_3);
  if(val_1==LOW && val_2==LOW && val_3==LOW){
     server.send(200, "text/html", "<html><body><h1>indisponible</h1><p><a href=\"/\"><button>Retour</button></a></p></body></html>");
  }
  else {
     server.send(200, "text/html", "<html><body><h1>disponible</h1><p><a href=\"/\"><button>Retour</button></a></p></body></html>");
  }
}
