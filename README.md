# Création du projet 
Création du projet avec 
- vue create ecommerce (vuejs2.x avec babel et lint)
- cd ecommerce
- vue add vuetify (vuetify 2.x)
- vue add axios
- modification du fichier package.json pour mettre "no-unused-vars": "off" dans "rules" pour eslintConfig (sinon axios déclenche une erreur car pas de variable options)

Dans le terminal je fais :
`npm run serve`
pour tester que tout va bien

Dans src/plugins/vuetify.js je fais :

```
import 'material-design-icons-iconfont/dist/material-design-icons.css'
import '@fortawesome/fontawesome-free/css/all.css'
```

et je rajoute 

```
icons: {
    iconfont: 'md' || 'fa' || 'mdi', // 'mdi' || 'mdiSvg' || 'md' || 'fa' || 'fa4' || 'faSvg'
},
```

Au préalable je dois installer les fonts concernées en suivant le tuto sur [le site vuetify](https://v2.vuetifyjs.com/en/features/icon-fonts/#installing-icon-fonts) :
- npm install @mdi/font -D
- npm install @fortawesome/fontawesome-free -D
- npm install material-design-icons-iconfont -D

Dans src/App.vue j'enlève le composant HelloWorld

Avec Axios je récupère le JSON que je met dans this.products qui va être la variable qui stocke tous les produits
J'initialise aussi un this.sortedProducts qui va être mes produits filtrés / triés

Pour le design je me suis inspiré de [ce tuto YouTube](https://www.youtube.com/watch?v=Tne-mbA1km4&t=200s) pour : 

J'ai commencé par récupérer mes produits et les afficher 
Ensuite j'ai créé mon premier filtre (Prix), j'ai ajouté un watcher pour déclencher une fonction quand la valeur change
Cette fonction filtre sortedProducts pour garder les prix qui sont entre les limites mini et maxi 

J'ai ajouté un évenement @click sur le bouton de fermeture de "prix" pour réinitialiser le prix à la valeur par défaut.

J'ai ajouté ensuite mes autres filtres

Enfin j'ai terminé avec le tri

Le design et le responsive ne sont pas optimum, j'avais cours (avec un gros projet) la première semaine de vacances et la deuxième semaine je devais passer mon perfectionnement BAFA option surveillant de baignade du dimanche au dimanche j'étais dehors de 6h à 20h, il a donc fallut que je me forme à VueJS dans les transports et le soir en rentrant.

J'espère que le projet vous conviendra.