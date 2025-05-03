# The Refiner Game

**A Wikimedia Commons micro-contributions tool for refining "depicts" statements in Structured Data on Commons (SDC).**  
This project aims to make it easy and engaging—especially on mobile—to improve the precision of image metadata on Wikimedia Commons by replacing generic "depicts" tags with more specific ones.

---

## 🧠 What Is This?

**The Refiner Game** is a small but meaningful tool in the space of *serious games for knowledge contribution*. It allows users to perform **micro-edits** on [Structured Data on Commons (SDC)](https://commons.wikimedia.org/wiki/Commons:Structured_data), refining the metadata of media files—especially their `depicts` statements (`P180`).

Think of it like this: if a picture on Commons depicts a "dog", but the specific breed is visually identifiable (e.g., a Golden Retriever), this game helps you quickly **replace the generic label ("dog") with a more specific one ("Golden Retriever")**. That kind of granularity is vital for data reusability, semantic accuracy, and better search results across Wikimedia platforms.

---

## 🕹️ How It Works

1. **User selects a category** (Wikidata item), such as `dog (Q144)`, to focus on.
2. The app:
   - Queries Commons for media files that depict this item (e.g. dogs).
   - Retrieves direct subclasses of the selected item from Wikidata.
   - Ranks those subclasses based on how many times they are used as instances in Wikidata (e.g., which dog breeds are most commonly used).
3. For each media file:
   - The user sees the current "depicts" tag.
   - A ranked list of potential more-specific subclasses is shown as suggestions.
   - The user can pick a better one (or skip).
   - When submitted:
     - The old "generic" depict statement is removed.
     - The more specific subclass is added in its place.

This process contributes to the overall improvement of structured image metadata.

---

## 🔧 Technical Overview

This is a **client-side web app**, designed to be mobile-friendly and fast. It uses:
- **SPARQL queries** to:
  - Get media files with a given `depicts` value.
  - Retrieve subclasses of a Wikidata item, ranked by instance usage.
- **Wikimedia OAuth** (client-side) for:
  - User login
  - Performing edits (removing generic `depicts`, adding specific subclass)
- **Wikidata search API** to:
  - Autocomplete and populate the initial item/category selector.

### SPARQL Queries

Two key queries are used:

#### 1. Media files depicting a specific item:

```sparql
SELECT ?file ?fileLabel ?image WHERE {
  ?file wdt:P180/wdt:P279* wd:QXXX.
  ?file schema:contentUrl ?image.
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
LIMIT 100
```

#### 2. Subclasses of an item, ranked by usage:

```sparql
SELECT ?subclass ?subclassLabel ?subclassDescription (COUNT(DISTINCT ?instance) AS ?instanceCount) WHERE {
  ?subclass wdt:P279 wd:QXXX.
  ?subclassBelow wdt:P279* ?subclass.
  ?instance wdt:P31 ?subclassBelow.
  SERVICE wikibase:label {
    bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en".
    ?subclass rdfs:label ?subclassLabel.
    ?subclass schema:description ?subclassDescription.
  }
}
GROUP BY ?subclass ?subclassLabel ?subclassDescription
ORDER BY DESC(?instanceCount)
``` 

Replace QXXX with the Q-ID of the concept to refine (e.g. Q144 for dog).

## UI Components
	•	Search Field: Uses Wikidata API to select the concept to refine (e.g. “dog”).
	•	Suggestions List: Shows subclasses with label + description. Supports filtering and scrolling.
	•	Edit Interface:
	•	Show current depiction(s)
	•	Allow selecting a better subclass
	•	Submit change via OAuth-authenticated edit
	•	Option to skip an image

⸻

## 🏁 Goals
	•	Make it easy to contribute micro-edits from mobile or desktop
	•	Promote specificity in Commons structured data
	•	Gamify or streamline repetitive but meaningful contributions
	•	Encourage participation from both casual users and seasoned Wikimedians

⸻

## 📈 Why It Matters

In Wikimedia culture, more precise data leads to:
	•	Better reuse
	•	More discoverable media
	•	Smarter queries
	•	Cleaner knowledge graphs

For instance:
	•	“Depicts: mammal” → too vague
	•	“Depicts: dog” → better
	•	“Depicts: Golden Retriever” → great
	•	“Depicts: Boomer (Golden Retriever, Q123456)” → perfect!

⸻

## 🔐 OAuth Notes
	•	The app will authenticate users via Wikimedia OAuth.
	•	Only client-side tokens will be used.
	•	Edit actions will use OAuth permissions to update P180 statements via the Commons API.

⸻

## 📦 Project Structure (Planned)

```
.
├── index.html             # Main frontend
├── src/
│   ├── app.js             # App logic
│   ├── sparql/
│   │   ├── getMedia.sparql
│   │   └── getSubclasses.sparql
│   └── oauth.js           # OAuth handling
├── public/
│   └── assets/            # Icons, styling
├── README.md
└── LICENSE
```


⸻

## 🤝 Contributions

Pull requests welcome! The goal is to keep this tool lightweight, reusable, and understandable by contributors in the Wikidata/Commons ecosystem. If you’re familiar with front-end dev, SPARQL, or Wikimedia APIs, you’ll feel at home. All suggestions and ideas are welcome!

⸻

## 📜 License

MIT

⸻

## 📬 Contact

Feel free to reach out via the relevant Wikimedia developer channels or file an issue if you’d like to help or have suggestions. All suggestions and ideas are welcome!