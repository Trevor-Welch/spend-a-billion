<template>
	<div class="spend-a-billion">
		<div class="budget">
			<div class="container">
				<h2>Budget</h2>
				<p v-bind:class="{ overbudget: remaining_balance < 0 }">{{remaining_balance | currency}}</p>
			</div>
		</div>
		<h1>Spend One Billion</h1>
		<p>Recently, we've been asking the question <a href="https://www.nytimes.com/2019/10/15/us/politics/us-billionaires.html">"Should Billionares Exist?"</a>.</p> 
		<p>To help make a choice on if someone should be allowed to be a billionaire, this page will allow you to be one for a day and budget what you could afford if you only had <i>one</i> billion dollars.</p>
		<p>Original idea was created by <a href="https://twitter.com/nealagarwal">Neal Agarwal</a> and his page <a href="https://neal.fun/spend/">Spend Bill Gates' Money</a>.</p>

		<br>
		<br>

		<div class="category" v-for="category in options.categories">
			<div class="toggle-bar" @click="toggleShow(category)">
				<h2>{{ category.name }}</h2>
				<i class="fas fa-chevron-down"></i>
			</div>
			<div class="subcategory" v-for="subcategory in category.subcategories" v-if="category.show_contents">
				<div class="toggle-bar" @click="toggleShow(subcategory)">
					<h3>{{ subcategory.name }}</h3>
					<i class="fas fa-chevron-down"></i>
				</div>
				
				<div class="option" v-for="option in subcategory.options" v-if="subcategory.show_contents">
					<div class="bar">
						<h4 class="title" @click="toggleShow(option)">{{option.title}}</h4>
						<span class="cost">{{option.cost | currency}}</span>
						<div class="quantity-container">
							<button @click="decrease(option)">-</button>
							<input type="text" class="quantity" v-model="option.quantity">
							<button @click="increase(option)">+</button>
						</div>
						
					</div>
					<p class="description" v-if="option.show_contents">{{option.description}}</p>
				</div>
			</div>
		</div>

		<div v-if="(shopping_list.length > 0) && (remaining_balance >= 0)">
			<h2>Results</h2>
			<p>You were able to afford:</p>
			<ul>
				<li v-for="option in shopping_list">
					<div v-if="option.quantity >= 2">{{option.quantity}} {{option.multiple}}</div>
					<div v-if="option.quantity == 1">{{option.title}}</div>
				</li>
			</ul>
			<p v-if="remaining_balance > 0">Afterwards, you still had {{remaining_balance | currency}} remaining.</p>
		</div>
		
	</div>
</template>

<style scoped lang="scss">
.budget {
	width: 180px;
	display: block;
	position: fixed;
	text-align: center;
	top:0;
	left: 50%;
	margin-left: -90px;
	filter: drop-shadow(-1px 2px 2px rgba(50, 50, 0, 0.1));
	.container{ clip-path: polygon(0 0, 100% 0, 90% 100%, 10% 100%); background: white; padding: 4px 0px;}
	h2 {
		margin: 0px auto;
		font-size: 12px;
		text-transform: uppercase;
		font-weight: 500;
	}
	p {
		font-weight: bold;
		margin: 0px auto;
		margin-top: 4px;
		color: #4CAF50;
	}
	.overbudget {
		color: red;
	}
}

.spend-a-billion {
	text-align: left;
	max-width: 800px;
	box-sizing: border-box;
	padding: 8px;
	margin: 0px auto;
	&>p{
		line-height: 1.5em;
	}
	h1{ margin-bottom: 48px; }
	.category {
		.subcategory {
			box-sizing: border-box;
			padding-left: 16px;
			.option {
				box-sizing: border-box;
				padding-left: 16px;
				margin-bottom: 32px;
				.description {
					margin: 0px;
					margin-top: 4px;
					box-sizing: border-box;
					padding: 8px 16px;
					color: #757575;
				}
				.bar {
					display: flex;
					justify-content: space-between;
					align-items: center;
					.title, .cost, .quantity {
						display: inline-block;
						padding: 4px 8px;
					}
					.title {
						width: 100%;
						background: #FFF;
						border-radius: 4px;
						box-sizing: border-box;
						padding: 8px 16px;
						margin: 0px;
						transition: 0.2s;
						cursor: pointer;
						font-weight: 500;
						&:hover {
							background-color: #F5F5F5;
						}
					}

					.cost {
						font-weight: bold;
					}

					@media screen and (max-width: 750px){
						.title{display: block;}
						.cost{text-align: center; display: block; width: 100%;}
						.quantity-container{ width: 100%;  justify-content: center;}
						flex-wrap: wrap;
						text-align: center;
					}

					button {
						background: #FFF;
						border: 1px solid #E0E0E0;
						border-radius: 4px;
						padding: 8px 16px;
						transition: 0.2s;
						cursor: pointer;
						margin: 0px 4px;
						&:hover {
							background-color: #F5F5F5;
						}
					}
					.quantity-container {
						display:flex;
					}
					.quantity {
						max-width: 80px;
						text-align: right;
						background: #FFF;
						border: 1px solid #E0E0E0;
						border-radius: 4px;
						padding: 8px 16px;
						transition: 0.2s;
						cursor: pointer;
						margin: 0px 4px;
						font-size: inherit;
						font-family: inherit;
						&:hover {
							background-color: #F5F5F5;
						}
					}
					
				}
			}
		}
	}
}

.toggle-bar {
	margin-bottom: 8px;
	display: flex;
	box-sizing: border-box;
	border: 1px solid #E0E0E0;
	border-radius: 4px;
	padding: 8px 16px;
	align-items: center;
	justify-content: space-between;
	cursor: pointer;
	background-color: #FFFFFF;
	transition: 0.2s;
	h2, h3, h4 { margin: 0px; }
	&:hover {
		background-color: #F5F5F5;
	}
}

</style>

<script>
	export default {
		name: "SpendABillion",
		filters: {
			currency: function(value) {
				return "$" + value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
			},
			commas: function(value) {
				return value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
			}
		},
		methods: {
			increase: function(option) {
				option.quantity++
			},
			decrease: function(option) {
				if(option.quantity > 0){
					option.quantity--
				}
			},
			toggleShow: function(element) {
				element.show_contents = !element.show_contents
			}
		},
		computed: {
			shopping_list: function() {
				let options = this.options
				let shopping_list = []
				for(let category in options.categories){
					category = options.categories[category]
					for(let subcategory in category.subcategories){
						subcategory = category.subcategories[subcategory]
						for(let option in subcategory.options){
							option = subcategory.options[option]
							if(option.quantity > 0){
								shopping_list.push(option)
							}
						}
					}
				}
				return shopping_list
			},
			remaining_balance: function() {
				let expenses = 0
				let shopping_list = this.shopping_list
				for(let option of shopping_list){
					expenses += option.quantity * option.cost
				}
				return this.budget - expenses
			}
		},
		data: function () {
			return {
				budget: 1000000000,
				options: {
					categories: [
					{
						name: "Shelter",
						show_contents: false,
						subcategories: [
						{
							name: "Apartments",
							show_contents: false,
							options: [
							{
								title: "One Month of Rent - 1 Bedroom Apartment in New York City",
								multiple: "Months of Rent - 1 Bedroom Apartment in New York City",
								show_contents: false,
								description: "Living in the big city is not cheap. The Zumper National Rent Report for November 2019 listed this as the median cost for a one bedroom.",
								cost: 3000,
								quantity: 0
							},
							{
								title: "One Year of Rent - 1 Bedroom Apartment in New York City",
								multiple: "Years of Rent - 1 Bedroom Apartment in New York City",
								show_contents: false,
								description: "Living in the big city is not cheap. The Zumper National Rent Report for November 2019 listed this as the median cost for a one bedroom.",
								cost: 36000,
								quantity: 0
							},
							{
								title: "One Month of Rent - 1 Bedroom Apartment (USA Average)",
								multiple: "Months of Rent - 1 Bedroom Apartment (USA Average)",
								show_contents: false,
								description: "",
								cost: 1024,
								quantity: 0
							},
							{
								title: "One Year of Rent - 1 Bedroom Apartment (USA Average)",
								multiple: "Years of Rent - 1 Bedroom Apartment (USA Average)",
								show_contents: false,
								description: "",
								cost: 12288,
								quantity: 0
							},
							{
								title: "One Month of Rent - 3 Bedroom Apartment (USA Average)",
								multiple: "Months of Rent - 3 Bedroom Apartment (USA Average)",
								show_contents: false,
								description: "",
								cost: 1640,
								quantity: 0
							},
							{
								title: "One Year of Rent - 3 Bedroom Apartment (USA Average)",
								multiple: "Years of Rent - 3 Bedroom Apartment (USA Average)",
								show_contents: false,
								description: "",
								cost: 19680,
								quantity: 0
							},
							]

						},
						{
							name: "Houses",
							show_contents: false,
							options: [
							{
								title: "Three Bedroom House in West Virginia",
								multiple: "Three Bedroom Houses in West Virginia",
								show_contents: false,
								description: "West Virginia has the lowest prices in all of America for housing with this being the average cost in 2019",
								cost: 96300,
								quantity: 0
							},
							{
								title: "One Bedroom House in Hawaii",
								multiple: "One Bedroom Houses in Hawaii",
								show_contents: false,
								description: "Hawaii has some of the most expensive houses in America with this price as the average for a one bedroom",
								cost: 618000,
								quantity: 0
							},
							{
								title: "A Median House located in Silicon Valley",
								multiple: "Median Houses located in Silicon Valley",
								show_contents: false,
								description: "Even tech workers are struggling to afford to own a house.",
								cost: 1600000,
								quantity: 0
							},
							]
						},
						{
							name: "Mansions",
							show_contents: false,
							options: [
							{
								title: "The Highlands Ranch Mansion",
								multiple: "Highlands Ranch Mansions",
								show_contents: false,
								description: "Located in Colorado",
								cost: 13000000,
								quantity: 0
							},
							{
								title: "The Monticello",
								multiple: "Monticellos",
								show_contents: false,
								description: "Located in Charlottesville, Virginia",
								cost: 36000000,
								quantity: 0
							},
							{
								title: "The Mark Twain House",
								multiple: "Mark Twain Houses",
								show_contents: false,
								description: "Located in Hartford, Connecticut",
								cost: 16300000,
								quantity: 0
							},
							{
								title: "The Ringling Mansion",
								multiple: "Ringling Mansions",
								show_contents: false,
								description: "Located in Sarasota, Florida",
								cost: 21000000,
								quantity: 0
							},
							{
								title: "The Oheka Castle",
								multiple: "Oheka Castles",
								show_contents: false,
								description: "Located in Huntington, New York",
								cost: 22500000,
								quantity: 0
							},
							{
								title: "The Vanderbilt Mansion",
								multiple: "Vanderbilt Mansions",
								show_contents: false,
								description: "Located in Hyde Park, New York",
								cost: 36000000,
								quantity: 0
							},
							{
								title: "The Spelling Manor",
								multiple: "Spelling Manors",
								show_contents: false,
								description: "Located in Los Angeles, California",
								cost: 120000000,
								quantity: 0
							},
							]
						}
						]			
					},
					{
						name: "Utilities",
						show_contents: false,
						subcategories: [
						{
							name: "Essentials",
							show_contents: false,
							options: [
							{
								title: "One Month of Water",
								multiple: "Months of Water",
								show_contents: false,
								description: "Based on the average water bill for a low usage family in 2018",
								cost: 34,
								quantity: 0
							},
							{
								title: "One Month of Electricity",
								multiple: "Months of Electricity",
								show_contents: false,
								description: "Based on the average electricity bill for a family in 2018",
								cost: 104,
								quantity: 0
							},
							{
								title: "One Month of Gas",
								multiple: "Months of Gas",
								show_contents: false,
								description: "Based on the average gas bill for a family in 2018",
								cost: 83,
								quantity: 0
							},
							{
								title: "One Year of Utilities",
								multiple: "Years of Utilties",
								show_contents: false,
								description: "Based on the average cost for utilities for a family in 2018",
								cost: 2300,
								quantity: 0
							},
							]
						},
						{
							name: "Additional",
							show_contents: false,
							options: [
							{
								title: "One Month of Internet (Low Speed)",
								multiple: "Months of Internet (Low Speed)",
								show_contents: false,
								description: "Based on the average price of starting internet packages in 2019",
								cost: 45,
								quantity: 0
							},
							{
								title: "One Month of Internet (High Speed)",
								multiple: "Months of Internet (High Speed)",
								show_contents: false,
								description: "Based on the price of Google Fiber in 2019 (does not include the setup fee)",
								cost: 70,
								quantity: 0
							},
							]
						},

						]
					},
					{
						name: "Food",
						show_contents: false,
						subcategories: [
						{
							name: "Fast Food",
							show_contents: false,
							options: [
							{
								title: "A McDonalds Cheeseburger",
								multiple: "McDonalds Cheeseburgers",
								show_contents: false,
								description: "It may not be the most nutritious, but it only costs a dollar.",
								cost: 1,
								quantity: 0
							},
							{
								title: "A McDonalds Big Mac Meal",
								multiple: "McDonalds Big Mac Meals",
								show_contents: false,
								description: "The iconic burger with a side of fries and a drink.",
								cost: 6,
								quantity: 0
							},
							{
								title: "An In-N-Out Double-Double Burger Meal",
								multiple: "In-N-Out Double-Double Burger Meals",
								show_contents: false,
								description: "I mean, what else are you gonna get?",
								cost: 7,
								quantity: 0
							},
							{
								title: "A Whataburger Meal",
								multiple: "Whataburger Meals",
								show_contents: false,
								description: "We had to include Whataburger, otherwise it would feel left out.",
								cost: 7,
								quantity: 0
							},
							{
								title: "A Shake Shack Shake",
								multiple: "Shake Shack Shakes",
								show_contents: false,
								description: "No shake like a Shake Shack shake. (say that five times fast.)",
								cost: 6,
								quantity: 0
							},
							]
						},
						{
							name: "Groceries",
							show_contents: false,
							options: [
							{
								title: "One Month of Groceries for an Average Person",
								multiple: "Months of Groceries for an Average Person",
								show_contents: false,
								description: "Average cost under a 'moderate' budget.",
								cost: 250,
								quantity: 0
							},
							{
								title: "One Year of Food for an Average Person",
								multiple: "Years of Food for an Average Person",
								show_contents: false,
								description: "Average cost under a 'moderate' budget.",
								cost: 250,
								quantity: 0
							},
							]
						}
						]
					},
					{
						name: "Transportation",
						show_contents: false,
						subcategories: [
						{
							name: "Air",
							show_contents: false,
							options: [
							{
								title: "A One-Way Ticket by Helicopter",
								multiple: "One-Way Tickets by Helicopter",
								show_contents: false,
								description: "This price is based on Gotham Air, per person",
								cost: 219,
								quantity: 0
							},
							{
								title: "One Domestic Ticket Round Trip (Economy)",
								multiple: "Months of Groceries for an Average Person",
								show_contents: false,
								description: "Average cost for a plane ticket according to the Department of Transportation in 2018.",
								cost: 346,
								quantity: 0
							},
							{
								title: "A Robinson R-22 Helicopter",
								multiple: "Robinson R-22 Helicopters",
								show_contents: false,
								description: "A 'cheap' private helicopter.",
								cost: 250000,
								quantity: 0
							},
							{
								title: "A Bell B206 JetRanger Helicopter",
								multiple: "Bell B206 JetRanger Helicopters",
								show_contents: false,
								description: "A medium-priced helicopter.",
								cost: 700000,
								quantity: 0
							},
							{
								title: "A Eurocopter EC120 Colibri Hummingbird Helicopter",
								multiple: "Eurocopter EC120 Colibri Hummingbird Helicopters",
								show_contents: false,
								description: "One of the best helicopters that money can buy.",
								cost: 1700000,
								quantity: 0
							},
							{
								title: "A Gulfstream G650 Private Jet",
								multiple: "Gulfstream G650 Private Jets",
								show_contents: false,
								description: "The absolute best private jet that money can buy.",
								cost: 65000000,
								quantity: 0
							},
							{
								title: "A Boeing 757 Aircraft",
								multiple: "Boeing 757s",
								show_contents: false,
								description: "Based on the price as paid by Donald Trump.",
								cost: 100000000,
								quantity: 0
							},
							]
						},
						{
							name: "Ground",
							show_contents: false,
							options: [
							{
								title: "A Tesla Model 3",
								multiple: "Tesla Model 3s",
								show_contents: false,
								description: "This is based on the 2019 model at MSRP",
								cost: 35000,
								quantity: 0
							},
							{
								title: "A Tesla Model S",
								multiple: "Tesla Model Ss",
								show_contents: false,
								description: "This is based on the 2019 model at MSRP",
								cost: 75000,
								quantity: 0
							},
							{
								title: "A Tesla Roadster",
								multiple: "Tesla Roadster",
								show_contents: false,
								description: "This is based on the announced price for the 2020 model",
								cost: 200000,
								quantity: 0
							},
							]
						}
						]
					},
					{
						name: "Expensive",
						show_contents: false,
						subcategories: [
						{
							name: "Luxury Items",
							show_contents: false,
							options: [
							{
								title: "One Manhattan Parking Space",
								multiple: "Manhattan Parking Spaces",
								show_contents: false,
								description: "Between 1978 and 2010, Manhattan lost 25,000 off-street public parking spaces, reports Apartmentality. And developers can only include parking spots for about 20% of new residences. A reliable parking spot has quickly become the new standard for luxury in NYC. In 2015, a boutique Manhattan condo, 15 Renwick, sold three spaces for $1 million apiece.",
								cost: 1000000,
								quantity: 0
							},
							{
								title: "One Bluefin Tuna",
								multiple: "Bluefin Tuna",
								show_contents: false,
								description: "Experts are worried about the future of tuna, officially a “threatened species.” But we’re still not sure if a single fish is worth a $3.1 million price tag. Kiyoshi Kimura, a sushi restaurant owner, paid that much for a bluefin tuna at Japan’s Toyosu market in early 2019, reports Reuters.",
								cost: 3100000,
								quantity: 0
							},
							{
								title: "A Neiman Marcus Limited Edition Fighter",
								multiple: "Neiman Marcus Limited Edition Fighters",
								show_contents: false,
								description: "Only 45 of the Neiman Marcus Limited Edition Fighter motorcycles exist. With a carbon-fiber frame and titanium features, this bike can hit 190 miles per hour. It first sold for $110,000 — not so pricy comparatively. However, enthusiasts love the unusual design and body, the latter carved from a single piece of metal. Now highly sought-after, “it’s our street-legal sci-fi dream come to life,” as Neiman Marcus first explained.",
								cost: 11000000,
								quantity: 0
							},
							{
								title: "The Graff Diamonds Hallucination Watch",
								multiple: "Graff Diamonds Hallucination Watches",
								show_contents: false,
								description: "Completed in 2014, the Hallucination watch by London-based Graff Diamonds took “several thousand hours of work” and 110 carats to create, reports Money Inc. “A sculptural masterpiece,” the platinum quartz watch is encrusted in yellow, pink, blue, gray, and orange diamonds in different cuts.",
								cost: 55000000,
								quantity: 0
							},
							{
								title: "A 1963 Ferrari 250 GTO",
								multiple: "1963 Ferrari 250 GTOs",
								show_contents: false,
								description: "In 2018, the vintage Italian sports car broke a record when Chicago’s David MacNeil, the founder/CEO of WeatherTech, bought it for $70 million. As CNN explains, “It’s extremely rare for an owner to part with one at any price” because only 39 were built by Ferrari between 1962 and 1964.",
								cost: 70000000,
								quantity: 0
							},
							{
								title: "Leonardo da Vinci’s Salvator Mundi",
								multiple: "Salvator Mundi Paintings",
								show_contents: false,
								description: "In 2017, Saudi Prince Bader bin Abdullah bin Mohammed bin Farhan al-Saud bought the Salvator Mundi (Savior of the World) at a Christie’s auction, shocking everyone with his final bid of $450 million. As Vanity Fair explains, “The last known da Vinci painting in private hands” first sold for $60 in 1958. Now, it had a home after the prince won the bidding war by telephone.",
								cost: 450000000,
								quantity: 0
							},
							]
						},
						
						]
					},
					]
				}
			}
		}
	}
</script>

