Excellent question ✅ — yes, you absolutely can print a **nicely aligned table** in Python **without manually specifying field widths (like 25 or 30)**.

There are three clean ways to do this:

1. Use the **`tabulate`** library → best for quick, pretty tables.
2. Use **`prettytable`**.
3. Use **Python built-in** string formatting with dynamic widths — but you asked *not to specify widths*, so `tabulate` is perfect.

Here’s an updated version using `tabulate` (recommended for readability and simplicity).

---

### **Updated Script: `mongo_dr_validate.py`**

```python
import argparse
from pymongo import MongoClient
from pymongo.errors import PyMongoError
from tabulate import tabulate

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

    # MongoDB vCore connection string (using Entra ID OIDC authentication)
    mongo_uri = f"mongodb+srv://{cosmos_account}.mongo.cosmos.azure.com:10255/?authMechanism=MONGODB-OIDC&tls=true"

    try:
        client = MongoClient(mongo_uri)
        db_names = client.list_database_names()
        results = []

        for db_name in db_names:
            if db_name in ("admin", "local", "config"):
                continue

            db = client[db_name]
            for coll_name in db.list_collection_names():
                count = db[coll_name].estimated_document_count()
                results.append([db_name, coll_name, count])

        if not results:
            print("No collections found.")
            return

        if output_format == "csv":
            print("Database,Collection,DocumentCount")
            for row in results:
                print(",".join(str(x) for x in row))
        else:
            print(tabulate(results, headers=["Database", "Collection", "Documents"], tablefmt="github"))
            print(f"\nTotal collections scanned: {len(results)}")

    except PyMongoError as e:
        print(f"❌ Error: {e}")

if __name__ == "__main__":
    main()
```

---

### **Install dependency**

```bash
pip install tabulate pymongo
```

---

### **Usage**

#### 📋 Pretty table format

```bash
python mongo_dr_validate.py --cosmos-account myCosmosAccount
```

**Sample Output:**

```
| Database   | Collection     |   Documents |
|-------------|----------------|-------------|
| inventory   | products       |       12450 |
| inventory   | suppliers      |        3580 |
| orders      | orderDetails   |        9042 |
| orders      | customers      |        5020 |

Total collections scanned: 4
```

#### 📄 CSV output

```bash
python mongo_dr_validate.py --cosmos-account myCosmosAccount --output-format csv > dr_validation.csv
```

---

✅ **Benefits**

* No manual width formatting.
* Table auto-adjusts based on content.
* Easily switch between table and CSV.
* Ideal for DR validation reports.

---

Would you like me to add an **optional `--compare` mode** next — where you can pass two clusters (primary & DR) and it automatically compares collection counts side-by-side and highlights mismatches?
