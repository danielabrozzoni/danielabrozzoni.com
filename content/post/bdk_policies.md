---
title: "Policying in BDK"
date: 2022-07-27T17:20:58+02:00
draft: true
---

```
fn create_wallet() -> Result<Wallet<sled::Tree>, Box<dyn Error>> {
    let external_descriptor = "wpkh(tprv8ZgxMBicQKsPdy6LMhUtFHAgpocR8GC6QmwMSFpZs7h6Eziw3SpThFfczTDh5rW2krkqffa11UpX3XkeTTB2FvzZKWXqPY54Y6Rq4AQ5R8L/84'/0'/0'/0/*)";

    let mut datadir = PathBuf::from_str("/tmp/")?;
    datadir.push(".bdk-example");
    let database = sled::open(datadir)?;
    let database = database.open_tree(".bdk-example-db".to_string())?;

       let wallet: Wallet<sled::Tree> = Wallet::new(
            external_descriptor,
            None,
            bitcoin::Network::Regtest,
            database,
        )?;

    Ok(wallet)
}
```
