<template>
  <div>
    <section class="heading">
      <div class="row center">
        <div class="column small-full medium-two-third">
          <h2>Did you know..</h2>
          <p>Little facts about CSS, html and other frontend related thingies..</p>
        </div>
      </div>
    </section>
    <section class="all-tags">
      <ul class="tags" v-if="tags.length > 0">
        <li class="tags__tag" v-for="(tag, index) in tags" :class="{ active : isActiveFilter(tag.title)}" :key="'taglist-'+index+Math.random(0,1)" :data-count="tag.count" >
          <a v-on:click="setFilter(tag.title)" href="#" class="tags__link" >{{tag.title}}</a>
        </li>
      </ul>
    </section>
    <section class="settings" v-if="stories.length > 0">
      <div class="row center">
        <div class="column small-full medium-two-third">
          showing <strong>{{filterStories(stories).length}}</strong> of <strong>{{stories.length}}</strong> articles.
        </div>
      </div>
    </section>

    <section class="stories" v-if="stories.length > 0">
      <div class="row center">
        <div class="column small-full medium-two-third" v-for="story in filterStories(stories)" :key="story.id">
          <h3 class="story__title">{{story.title}}</h3>
          <article class="story">
            <vue-markdown class="story__description" v-if="story.description">{{story.description}}</vue-markdown>
            <vue-markdown class="story__example" v-if="story.example">{{story.example}}</vue-markdown>
            <vue-markdown class="story__code" v-if="story.code">{{story.code}}</vue-markdown>
            <ul class="story__tags tags" v-if="story.tags.length > 0">
              <li class="tags__tag" v-for="(tag,index) in story.tags" :key="'tag-'+index">
                <a href="#" class="tags__link">{{tag}}</a>
              </li>
            </ul>
          </article>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import AppLogo from "~/components/AppLogo.vue";
import VueMarkdown from "vue-markdown";
import storiesData from "~/static/data/stories.json";

export default {
	components: {
		AppLogo,
		VueMarkdown
	},
	data() {
		return {
			stories: null,
			tags: [],
			henkiesverhalen: [],
			filters: []
		};
	},
	created() {
		this.stories = storiesData.stories;
	},
	methods: {
		existInTags: function(tag) {
			let _this = this;
			let Exists = false;
			_this.tags.forEach(function(item) {
				if (item.title === tag) {
					Exists = true;
				}
			});
			return Exists;
		},
		getIndexOfTag: function(tag) {
			let _this = this;
			let Index = null;
			let i = 0;
			_this.tags.forEach(function(item) {
				i++;
				if (item.title === tag) {
					Index = i;
				}
			});
			return Index;
		},
		isActiveFilter: function(tag) {
			let _this = this;
			if (_this.filters.indexOf(tag) > -1) {
				return true;
			} else {
				return false;
			}
		},
		filterStories: function(stories) {
			let _this = this;
			let passingStories = [];
			if (_this.filters.length < 1) {
				return stories;
			} else {
				stories.forEach(function(story) {
					if (_this.ifAny(story.tags, _this.filters)) {
						passingStories.push(story);
					}
				});
				return passingStories;
			}
		},
		ifAny: function(haystack, arr) {
			return arr.some(function(v) {
				return haystack.indexOf(v) >= 0;
			});
		},
		setFilter: function(filter) {
			let _this = this;
			if (_this.isActiveFilter(filter)) {
				_this.filters.splice(_this.filters.indexOf(filter), 1);
			} else {
				_this.filters.push(filter);
			}
		}
	},
	mounted() {
		let _this = this;

		// Initialize all tags
		_this.stories.forEach(function(story) {
			for (let i = 0; story.tags.length > i; i++) {
				if (_this.existInTags(story.tags[i])) {
					// console.log("bestaat alll");
					console.log();

					if (_this.tags[_this.getIndexOfTag(story.tags[i])]) {
						_this.tags[_this.getIndexOfTag(story.tags[i])].count =
							_this.tags[_this.getIndexOfTag(story.tags[i])].count + 1;
					}
					// console.log(_this.tags[story.tags[i].title]);
					// _this.tags[story.tags[i]].count = story.tags[i].count + 1;
				} else {
					_this.tags.push({ title: story.tags[i], count: 1 });
				}
			}
		});
	}
};
</script>

<style lang="scss">
@import "~henris/ext";

.heading {
	background-image: linear-gradient(
		to right bottom,
		color(Black),
		darken(Blue, 40%)
	);
	color: color(White);
	padding: grid(3 0);
	h2 {
		color: color(Blue);
	}
	@media #{$small-only} {
		padding: grid(5) 30px;
	}
}
.settings {
	padding: grid(0.5 0);
	font-size: 0.8em;
	box-shadow: 0 0 grid(5) 0 color(Black, 0.05);
	@media #{$small-only} {
		padding: 30px;
	}
}

%code {
	font-size: 0.8em;
	font-weight: 600;
	line-height: 1.5em;
}
.all-tags {
	background-color: color(White);
	padding: 1rem 0;
	overflow: auto;
	.tags {
		text-align: center;
		white-space: nowrap;
		&__tag {
			margin: 0.5rem;
			background-color: color(Offwhite);
		}
	}
}

.story {
	background-color: white;
	margin: grid(1) 0;
	margin-top: grid(0.5);
	// box-shadow: 0 0 grid(1) 0 color(Black, 0.1);
	padding: grid(1 1 0 1);
	border-radius: 0.5em;
	&__description,
	&__example {
		font-size: 1.2em;
		line-height: 1.75;
		margin-top: 1em;

		p {
			code {
				background-color: color(Offwhite);
				@extend %code;
				padding: 0.25em 0.5em;
				border-radius: 2px;
			}
		}
	}
	&__example {
		border: 1px solid color(Offwhite);
		padding: 1rem;
		display: flex;
		flex-wrap: wrap;
	}
	&__tags {
		width: 100%;
		text-align: right;
		padding: grid(0.5 1);
	}
	&__code {
		display: block;
		overflow: scroll;
		background-color: color(Black, 0.9);
		color: color(White);
		padding: grid(0.5 1);
		margin: -grid(1);
		margin-top: grid(0.5);
		margin-bottom: 0;
		@extend %code;
		font-family: "Courier New", Courier, monospace;
	}
}
.tags {
	&__tag {
		display: inline-block;
		border-radius: 2px;
		font-size: 0.8em;
		font-weight: bold;
		position: relative;
		& + .tags__tag {
			margin-left: 1rem;
		}
		&:before {
			content: "";
			width: 100%;
			height: 100%;
			background-color: color(Black, 0.1);
			position: absolute;
			left: 0;
			top: 0;
			transition: transform 0.3s;
			transform: scale(0, 1);
			transform-origin: 0 0;
		}
		&:hover {
			&:before {
				transform: scale(1, 1);
			}
		}
		&.active {
			background-color: color(Blue);
			&,
			.tags__link:before {
				color: color(White);
			}
		}
		&[data-count] {
			padding-right: 2rem;
			&:after {
				position: absolute;
				right: 0;
				top: 0;
				width: 2rem;
				background-color: color(Blue, 0.15);
				height: 100%;
				line-height: 1.5rem;
				content: attr(data-count);
				font-size: 0.75em;
			}
		}
	}
	&__link {
		display: block;
		position: relative;
		z-index: 1;
		padding: 0.5em 1em;
		text-decoration: none;
		&:before {
			content: "#";
			color: color(Blue);
		}
	}
}

.stories {
	padding: grid(2) 0;
	@media #{$small-only} {
		padding: 60px 30px;
	}
}

// Elements to use for graphiks

.color-dot {
	padding: 0.25em 0.75em;
	font-size: 0.75em;
	border-radius: 1.25em;
	display: inline-block;
	& + .color-dot {
		margin-left: 1em;
	}
}
.block {
	background-color: color(Offwhite);
	height: grid(5);
	margin: 1em;
	flex-grow: 1;
	.box {
		height: grid(1);
		width: grid(1);
		background-color: color(Blue);
		line-height: grid(1);
		border-radius: 3px;
		text-align: center;
	}
}
</style>
