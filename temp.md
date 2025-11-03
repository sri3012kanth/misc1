Perfect ✅ — you want a **Python script** that:

* Connects to a **MongoDB vCore (Azure Cosmos)** instance (via Entra ID or normal auth).
* Lists **all databases** and **their collections**.
* Prints a **formatted summary** including the **document count per collection** — useful for **DR (Disaster Recovery) validation** comparisons.

Here’s a clean and production-ready script.

---

### **Script: `mongo_dr_validate.py`**

```python
import argparse
from pymongo import MongoClient
from pymongo.errors import PyMongoError

def main():
    parser = argparse.ArgumentParser(
        description="List all databases and collections with document counts for DR validation"
    )
    parser.add_argument("--cosmos-account", required=True, help="Cosmos DB vCore account name")
    parser.add_argument("--output-format", choices=["table", "csv"], default="table",
                        help="Output format: table (default) or csv")
    args = parser.parse_args()

    cosmos_account = args.cosmos_account
    output_format = args.output_format

    # MongoDB vCore connection string using Entra ID OIDC auth
    mongo_uri = f"mongodb+srv://{cosmos_account}.mongo.cosmos.azure.com:10255/?authMechanism=MONGODB-OIDC&tls=true"

    try:
        client = MongoClient(mongo_uri)

        db_names = client.list_database_names()
        results = []

        for db_name in db_names:
            if db_name in ("admin", "local", "config"):  # Skip system databases
                continue

            db = client[db_name]
            collections = db.list_collection_names()

            for coll_name in collections:
                count = db[coll_name].estimated_document_count()
                results.append((db_name, coll_name, count))

        # Output formatting
        if output_format == "csv":
            print("Database,Collection,DocumentCount")
            for db_name, coll_name, count in results:
                print(f"{db_name},{coll_name},{count}")
        else:
            print(f"\n{'Database':<25} {'Collection':<30} {'Documents':>10}")
            print("-" * 70)
            for db_name, coll_name, count in results:
                print(f"{db_name:<25} {coll_name:<30} {count:>10}")
            print("-" * 70)
            print(f"Total collections scanned: {len(results)}\n")

    except PyMongoError as e:
        print(f"❌ Error: {e}")


if __name__ == "__main__":
    main()
```

---

### **Usage Examples**

#### 🧾 Table format (default)

```bash
python mongo_dr_validate.py --cosmos-account myCosmosAccount
```

**Sample Output:**

```
Database                  Collection                     Documents
----------------------------------------------------------------------
inventory                 products                            12450
inventory                 suppliers                            3580
orders                    orderDetails                         9042
orders                    customers                            5020
----------------------------------------------------------------------
Total collections scanned: 4
```

---

#### 📄 CSV format (for saving and comparing in DR validation)

```bash
python mongo_dr_validate.py --cosmos-account myCosmosAccount --output-format csv > dr_primary.csv
```

Then you can run the same script against your **DR cluster** and compare the two CSVs easily with diff tools.

---

### ✅ **Key Features**

* Works with **Entra ID OIDC authentication** (no password needed).
* Skips system DBs (`admin`, `local`, `config`).
* Supports **table** and **CSV** output for DR comparison.
* Fully parameterized and environment-agnostic.

---

Would you like me to extend this script to also **compare the counts between two clusters (primary vs DR)** automatically and highlight mismatches? That would make DR validation completely automated.
