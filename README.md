# condivisione
fix dell'app.py per persistenza log del lab07

# codice scritto da AI
from flask import Flask, jsonify
import os
import logging
from logging.handlers import RotatingFileHandler

app = Flask(__name__)

def setup_logging():
    log_dir = '/data'
    os.makedirs(log_dir, exist_ok=True)
    log_file = os.path.join(log_dir, 'app_events.log')
    
    formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
    )
    
    # File handler con rotazione
    file_handler = RotatingFileHandler(
        log_file, maxBytes=10*1024*1024, backupCount=5
    )
    file_handler.setFormatter(formatter)
    file_handler.setLevel(logging.INFO)
    
    # Console handler per docker logs
    console_handler = logging.StreamHandler()
    console_handler.setFormatter(formatter)
    
    app.logger.addHandler(file_handler)
    app.logger.addHandler(console_handler)
    app.logger.setLevel(logging.INFO)
    
    app.logger.info("Applicazione Flask avviata")

setup_logging()

@app.route("/health")
def health():
    app.logger.info("Health check richiesto")
    return jsonify({"status": "ok", "service": "demo-docker"})

if __name__ == "__main__":
    port = int(os.environ.get("PORT", "8000"))
    app.logger.info(f"Server in avvio su porta {port}")
    app.run(host="0.0.0.0", port=port)




#PASSAGGIO SUCCESSIVO
E' necessario dare i permessi 777 alla cartella data e al file app_events.log
chmod 777 ./logs/data
chmod 777 ./logs/data/app_events.log


a me cosi ha funzionato e mi ha portato i log sul file... almeno credo che siano i log corretti XD
