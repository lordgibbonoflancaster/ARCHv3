___
- *Classification* : #Langues , [[Langues]]
- *Liens* : 
- *Période(s)* : [[Ère Archaïque]], ~-200 à ~50
- *Localisation* : [[Archelaus]]
- *Nations et gouvernements concernés* : [[La Hiérarchie]], [[Aristocratie des Perles]]
- *Langue mère* : [[Archelan Œcuménique]]
- *Langues soeurs* : Inconnues
- *Langues filles* : [[Kae Ancien]], [[Beahéen Ancien]], [[Phaelite Ancien]], [[Forgerain Ancien]]
- *Influences linguistiques majeures* : Inconnues
- *Influences linguistiques mineures* : Inconnues
---

```dataviewjs
const cur = dv.current().file.name;
const MAX = 3; // nb d'ancêtres affichés

const nom = v => !v ? null :
  (v.path ? v.path.split("/").pop().replace(/\.md$/, "")
          : String(v).replace(/\[\[|\]\]/g, "").split("|")[0].trim()) || null;

const L = new Map(dv.pages().where(p => p.type === "langue")
                    .array().map(p => [p.file.name, p]));
const mere = n => nom(L.get(n)?.langue_mere);

// chaîne d'ancêtres : mère, puis grand-mère, etc.
const ancetres = [];
for (let c = mere(cur), vu = new Set([cur]); c && L.has(c) && !vu.has(c); c = mere(c)) {
  ancetres.push(c); vu.add(c);
}
const soeurs = [...L.keys()].filter(n => n !== cur && mere(cur) && mere(n) === mere(cur));
const filles = [...L.keys()].filter(n => mere(n) === cur);

const cell = (c, l) => {
  const t = c.traductions?.[l];
  if (!t?.forme) return "—";
  return t.ipa ? `${t.forme} /${t.ipa}/` : t.forme;
};
const multi = (c, langs) =>
  langs.map(l => [l, cell(c, l)]).filter(([, v]) => v !== "—")
       .map(([l, v]) => `${l} : ${v}`).join(" ; ") || "—";

const concepts = dv.pages('"Concepts"').where(p => p.type === "concept")
                   .sort(p => p.file.name, "asc");
const t = c => c.traductions?.[cur];
const anc = ancetres.slice(0, MAX);

dv.table(
  ["Concept", "Forme", "API", ...anc.map(a => `← ${a}`), "Sœurs", "Filles"],
  concepts.map(c => [
    c.file.link,
    t(c)?.forme ?? "—",
    t(c)?.ipa ? `/${t(c).ipa}/` : "—",
    ...anc.map(a => cell(c, a)),
    multi(c, soeurs),
    multi(c, filles),
  ])
);
```
