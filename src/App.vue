<template>
	<div class="container">
		<div class="config">
			<span>Nombre total de paires : 9</span> | <span>Nombre de tentatives : {{ nbTentative }} </span> |
			<span>Temps de jeu : {{ timestamp }} </span> |
			<span>Nombre de paires trouvées : {{ pairesTrouvées }} </span>
			<button disabled class="btn btn-danger" id="recommencer" @click.self="recommencer">Recommencer</button>
		</div>
		<div class="cards">
			<div class="fireworks" style="display: none">
				<h1 class="top">BRAVOOOOOOOO</h1>
			</div>
			<div class="card" v-for="(el, i) in pairesImages" :key="i" :data-id="el.id" @click.self="rotate">
				<div class="card__front">
					<div class="card-body">
						<img :src="el.img" alt="un animal" class="rounded" />
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	name: "App",
	data() {
		return {
			images: [
				require("./assets/imgCard/elephant.jpeg"),
				require("./assets/imgCard/gepard.jpeg"),
				require("./assets/imgCard/lapin.jpeg"),
				require("./assets/imgCard/loup.jpeg"),
				require("./assets/imgCard/renard.jpeg"),
				require("./assets/imgCard/rhino.jpeg"),
				require("./assets/imgCard/singe.jpeg"),
				require("./assets/imgCard/tiger.jpeg"),
				require("./assets/imgCard/tortue.jpeg"),
			],
			pairesImages: [],
			nbTentative: 0,
			paires: [],
			pairesTrouvées: 0,
			timestamp: 0,
			timer: null,
			startTimer: false,
		};
	},
	watch: {
		startTimer() {
			console.log("Je commence");
			if (this.startTimer) {
				let minutes = 0;
				let secondes = 0;
				this.timer = setInterval(() => {
					secondes++;
					if (secondes < 10) {
						secondes = "0" + secondes;
					}
					if (secondes >= 60) {
						secondes = 0;
						minutes++;
					}
					this.timestamp = minutes + " min " + secondes + " sec";
				}, 1000);
			}
		},
	},
	methods: {
		// le melange des cartes
		melange() {
			let createPairs = [];
			for (let i = 0; i < this.images.length; i++) {
				createPairs.push({ id: i, img: this.images[i] }, { id: i, img: this.images[i] });
			}
			this.pairesImages = createPairs.sort(() => 0.5 - Math.random());
		},
		// la rotation des cartes
		rotate(e) {
			this.startTimer = true;
			document.querySelector("#recommencer").removeAttribute("disabled");
			// retourner la carte
			//  retourner une autre carte
			// verifier si elles sont paires
			// si paires  ( inc nb paire trouver + tentative + timerstart)
			// sinon retourner les deux cartes ( inc tentative + timerstart)
			if (this.paires.length < 2) {
				e.target.classList.toggle("rotate");
				this.paires.push(e.target);
				console.log("je retourne la carte");
				if (this.paires.length === 2) {
					this.nbTentative++;
					if (this.paires[0].dataset.id === this.paires[1].dataset.id) {
						this.pairesTrouvées++;
						this.paires = [];
					} else {
						setTimeout(() => {
							this.paires[0].classList.remove("rotate");
							this.paires[1].classList.remove("rotate");
							this.paires = [];
						}, 1000);
					}
				}
			}
			if (this.pairesTrouvées === 9) {
				clearInterval(this.timer);
				document.querySelector("#recommencer").innerHTML = "Rejouer";
			}
			if (this.pairesTrouvées === 1) {
				document.querySelector(".fireworks").style.display = "block";
			}
		},
		// restart
		recommencer(e) {
			this.nbTentative = 0;
			this.nbTrouvées = 0;
			this.timestamp = 0;
			e.target.setAttribute("disabled", "");
			clearInterval(this.timer);
			this.reset();
			this.melange();
		},
		// init
		reset() {
			Array.from(document.querySelectorAll(".card")).forEach((card) => {
				card.classList.remove("rotate");
			});
		},
	},
	mounted() {
		this.melange();
	},
	components: {},
};
</script>

<style lang="scss" src="./assets/app.scss"></style>

// perfectionner
