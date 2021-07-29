<template>
  <div id="app">
    <div class="container">
      <button class="btn btn-primary mb-3" @click="show = !show" >Click me!</button>
    
      <transition name="fade"> <!--(whatever name attribute you set it to is what you will use when styling it for 'enter,leave,leave-active)Animation can only be performed on a single element when inside the transition tag -->
        <div class="alert alert-danger" v-if="show">Show up now</div>
      
      </transition>

      <transition name="slide"> <!--(whatever name attribute you set it to is what you will use when styling it for 'enter,leave,leave-active)Animation can only be performed on a single element when inside the transition tag -->
        <div class="alert alert-danger" v-if="show">Show up for slide now</div>
      
      </transition>
      <transition name="slide" appear=""> <!--(whatever name attribute you set it to is what you will use when styling it for 'enter,leave,leave-active)Animation can only be performed on a single element when inside the transition tag -->
        <div class="alert alert-danger" v-if="show">Show up now</div>
      </transition>

      <div>
        <!-- <form action=""> -->
        <input class="border-red-light form-control" type="text" v-model="newContact" placeholder="Name">
        <button class="btn btn-success mt-3 form-control" @click="addContact">Add Contact</button>
        <button class="btn btn-outline-primary mt-2 form-control" @click="sortContacts">Sort Contact</button>
        <!-- </form> -->
        <transition-group name="slide-up" tag="ul" appear > <!--Performing transitions for group element-->
          <li class="font-serif text-lg" style="text-decoration:none;" v-for="contact in contacts" :key="contact">
            {{contact}}
          </li>
        </transition-group>
      </div>
    <button @click="isOpen = !isOpen" class="btn btn-warning">
      Profile
    </button>
    <transition
      @before-enter="beforeEnter"
      @enter="enter"
      @leave="leave"
      :css="false"
    >
      <div v-if="isOpen" class="drawer">
        <img src="./assets/user.png" class="mb-3" alt="avatar">
        <div style="width:200px;height:40px;" class="bg-grey-darker"></div>
        <div style="width:200px;height:40px;" class="mt-2 bg-grey-darker"></div>
        <div style="width:200px;height:40px;" class="mt-2 bg-grey-darker"></div>
        <div style="width:200px;height:40px;" class="mt-2 bg-grey-darker"></div>
      </div>
      
    </transition>

    <transition appear @before-enter="beforeEnterGsap" @enter="enterGsap" :css="false">
      <div class="card w-32">
        <h1>Trying this out with GSAP</h1>
      </div>
    </transition>
    <div class="flex justify-around">
    <div class="bg-red-light w-32 h-24 gsapCard"></div>
    <div class="bg-red-light w-32 h-24 gsapCard"></div>
    <div class="bg-red-light w-32 h-24 gsapCard"></div>
    <div class="bg-red-light w-32 h-24 gsapCard"></div>
    <div class="bg-red-light w-32 h-24 gsapCard"></div>
    </div>

    <!--STATE WITH GSAP -->
    <div :style="{width : tweenedNumber + 'px'}" class="bar mt-3 bg-red-darkest">
      <span>{{tweenedNumber.toFixed(0)}}</span>
    </div>


    </div>
  </div>
</template>


<script>
import Velocity from 'velocity-animate'
import gsap from 'gsap'
export default {
  name: 'App',
  components: {
  
  },
  data() {
    return {
      show: false,
      isOpen:false,
      newContact:'',
      contacts: ['Eli','Shukura','Lawal','Austin'],
      number:0,
      tweenedNumber: 0
    }
  },
  watch:{ //allows to watch a reactive value and do things when that value changes
    number(newValue){
      //animate our data
      gsap.to(this.$data,{
        duration:1,
        ease:'circ.out',
        tweenedNumber: newValue
      })
    }

  },
  methods:{
    addContact(){
      this.contacts.push(this.newContact)
      this.newContact = '';
    },
    sortContacts(){
      this.contacts.sort(); //Sort the list alphabetically
    },

    //Usingjs hooks for animation..visit velocityjs.org to get all the easing options available when you click on the 'jQueryUI'easings' link
    beforeEnter(el){
      el.style.opacity = 0
      el.style.width = '0em'
    },
    enter(el, done){
      Velocity(
        el,
        {opacity: 1, width: '12em'},
        {duration:2000, easing: 'easeOutCubic', complete: done}
      )
    },
    leave(el,done){
      Velocity(
        el,
        {opacity:0, width:'0em'},
        {duration:2000, easing: 'easeInCubic', complete:done}
      )
    },

//Trying new shit with GSAP
    beforeEnterGsap(el){
      el.style.opacity = 0
      el.style.transform = 'scale(0,0)'
    }, 
    enterGsap(el,done){
      gsap.to(el, {
        duration: 1,
        opacity: 1,
        scale:1,
        rotation:27,
        onComplete:done,
        ease:'bounce.out',
        
      })
    },
    randomNumber(){
      this.number = Math.floor(Math.random() * (800 -0))
    }

  },
  created(){
    setInterval(this.randomNumber, 1500)
  },
  mounted(){
    gsap.from('.gsapCard',{
      duration:0.5,
      opacity:0,
      scale:0,
      y:200,
      ease: 'power',
      stagger:{
        each: 0.1,
        from:'edges'
      }
    })
  }
}
</script>

<style>
.fade-enter{
  opacity: 0;
}
.fade-enter-active{
  transition: opacity 1s;
}
.fade-leave-to{
  opacity: 0;
}
.fade-leave-active{
  transition: opacity 3s;
  opacity: 0;

}

.slide-up-enter{
  transform: translateY(10px);
  opacity: 0;
}
.slide-up-enter-active{
  transition: all 2s ease;
}
.slide-up-move{ /* animation occurs when list changes in sort */
  transition: transform .5s ease-out;
}



.slide-enter{
  transform: translateY(20px);
}

.slide-enter-active{
  animation: slide-in 1s ease-out forwards; /*'forwards'this means it stays in the finishing position, it doesn't snap back to the start */
}

.slide-leave{
  /*No need to specify anything here because the starting position of the element is the default  position of the element*/
}
.slide-leave.active{
  animation: slide-out 1s ease-out forwards;
}

@keyframes slide-in {
  from {
    transform: translateY(20px);
  }
  to {
    transform: translateY(0);
  }
}
  
@keyframes slide-out {
  from {
    transform: translateY(0);
  }
  to {
    transform: translateY(20px);
  }
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
