<script>
    import bgOverlay from '$lib/assets/BG.png';
    import ebay from '$lib/assets/ebay.png';
    import cnn from '$lib/assets/cnn.png';
    import google from '$lib/assets/google.png';
    import cisco from '$lib/assets/cisco.png';
    import airbnb from '$lib/assets/airbnb.png';
    import uber from '$lib/assets/uber.png';
    import faqIcon from '$lib/assets/faqIcon.png';

    let modalOpen = false;

    let openModal = () =>{
        modalOpen = true;
    }

    let closeModal = () =>{
        modalOpen = false;
    }

    let toggleFaq = (nth) =>{
        let allDivs = document.querySelectorAll('.faq-container');
        var children = allDivs[nth-1].children;

        children[1].children[1].style.transform = children[1].children[1].style.transform == 'rotate(180deg)' ? 'rotate(0deg)' : 'rotate(180deg)';

        children[1].classList.toggle('closed');
    }

    let joinWaitlist = async (e) =>{
        window.location.href = "/"
    }  

    
</script>

<div class="w-screen h-screen darkGreen flex flex-col items-center overflow-auto">
    <img class="w-full h-full mix-blend-overlay absolute z-10" src={bgOverlay} />

    {#if modalOpen}
        <div class="modal-container">
            <div class="modal rounded-3xl z-50 darkest fullOpacity absolute w-5/6 md:w-4/6 lg:w-1/2 h-1/2 top-1/2 -translate-y-1/2 px-5 py-6 flex flex-col gap-1 items-center">
                <p class="text-white text-5xl md:text-6xl medium">Thank You for Joining the Waitlist!</p>
                <p class="text-gray-400 text-xl md:text-2xl medium w-full">Exciting news is on the horizon - we're gearing up to release the full website very soon!</p>
                <button class="shadow-2xl absolute bottom-6 right-8 light-green-bg text-black transform p-4 rounded-xl self-end font-semibold" on:click={closeModal}>Close</button>
            </div>
        </div>
    {/if}

    <div class="darkest py-3 nav mt-6 rounded-full border border-gray-600 flex items-center px-7 justify-between">
        <p class="light-green-text header text-2xl">Nearest<span class="ml-1 normal-text"> Parking Spot</span></p>
        <a class="light-green-text join-link" href="">Find the Nearest Parking Spot</a>
    </div>

    <div class="w-full px-9 md:px-20">
        <h1 class="light-green-text mt-8 md:mt-16">Did you know over 18 billion pounds of carbon emissions are created by people looking for parking spaces?<!--<span class="flex flex-col justify-center"><div class="cool-border"><div class="inner-cool-border"></div></div> hard as rock.</span>--></h1>
        <h2 class="text-gray-400 w-full mb-8 mt-3">Users reduce their carbon footprint by 5% per month on average when using out site.</h2>
    

        <div class="mt-20 relative z-20 w-full md:w-1/2 foldInput bg-transparent border border-gray-500 rounded-full flex">
            <input class="pr-2 flex-1 h-full bg-transparent z-20 md:placeholder:text-xl pl-4" type="text" placeholder="Email address">
            <button on:click={joinWaitlist} class="whitespace-nowrap z-50 h-full px-4 md:px-8 rounded-full light-green-border light-green-text darkest underline join-btn md:text-xl">
                Find a Parking Spot
            </button>
        </div>
    </div>

</div>

<div class="w-screen py-20 gap-24 light-green-bg flex flex-col items-center justify-center h-fit">
    <img class="w-full h-1/2 mix-blend-overlay absolute z-10" src={bgOverlay} />
    <div class="flex md:w-5/6 second-top-height flex-col md:flex-row px-4 items-center">
        <section class=""> 
            <div class=""> 
            <div class="text-gray-500"> 
                <p class="normal-text mb-4 tracking-tight font-bold text-4xl">Tackling urban air pollution with smart parking solutions</p> 
                <p class="mb-4 font-light normal-text text-2xl">Air pollution is a major problem in cities worldwide, contributing to climate change and negatively impacting human health. A major source of urban air pollution comes from vehicles, including exhaust emissions and congestion-related idling.</p> 
                <p class="mb-4 font-medium normal-text text-xl">Our app helps combat air pollution by using real-time data to guide drivers right to open parking spots near their destination. This reduces endless circling and driving around crowded areas. By cutting time spent searching for parking, our app decreases vehicle emissions and improves air quality.</p> 
                <a href="/" class="inline-flex items-center font-medium text-primary-600 hover:text-primary-800 text-lg">
                Find a Parking Spot
                <svg class="ml-1 w-6 h-6" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" d="M7.293 14.707a1 1 0 010-1.414L10.586 10 7.293 6.707a1 1 0 011.414-1.414l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414 0z" clip-rule="evenodd"></path></svg>
                </a> 
            </div> 
        </div> 
    </section>
    </div>
</div>

<div class="w-screen h-5/6 darkGreen md:px-32 py-24 flex flex-col items-center gap-8 overflow-hidden">
    <h5 class="light-green-text faq text-4xl w-5/6 md:w-4/6">Frequently Asked Questions</h5>
    <div class="z-30 faq-container light-green-text w-5/6 md:w-4/6" on:click={()=>{toggleFaq(1)}}>
        <h6 class="faq-title text-2xl">How does the software know where open parking spaces are?</h6>
        <div class="w-full flex items-center justify-between mb-4">
            <p class="faq-answer text-md mt-3 w-5/6">The app uses real-time data from a sensor network that detects when parking spots are vacant or occupied. Sensors like cameras and in-ground magnetometers feed data to our platform.</p>
            <img class="h-14 faq-icon" src={faqIcon} alt="FAQ Icon">
        </div>
        <hr>
    </div>

    <div class="z-30 faq-container light-green-text w-5/6 md:w-4/6" on:click={()=>{toggleFaq(2)}}>
        <h6 class="faq-title text-2xl">Does the app work for both street parking and parking lots/garages?</h6>
        <div class="w-full flex items-center justify-between mb-4 closed">
            <p class="faq-answer text-md mt-3 w-5/6">Yes, our app locates open parking in metered street spots, public parking garages, private lots, and more so you can quickly find the most convenient parking.</p>
            <img class="h-14 faq-icon" style="transform: rotate(180deg);" src={faqIcon} alt="FAQ Icon">
        </div>
        <hr>
    </div>

    <div class="z-30 faq-container light-green-text w-5/6 md:w-4/6" on:click={()=>{toggleFaq(3)}}>
        <h6 class="faq-title text-2xl">Is the parking availability data 100% accurate?</h6>
        <div class="w-full flex items-center justify-between mb-4 closed">
            <p class="faq-answer text-md mt-3 w-5/6">The data is generally highly accurate, but we cannot guarantee it reflects the latest status in real-time. We're continuously working to improve reliability.</p>
            <img class="h-14 faq-icon" style="transform: rotate(180deg);" src={faqIcon} alt="FAQ Icon">
        </div>
        <hr>
    </div>

    <div class="z-30 faq-container light-green-text w-5/6 md:w-4/6" on:click={()=>{toggleFaq(4)}}>
        <h6 class="faq-title text-2xl">Is the app available in other cities besides this one?</h6>
        <div class="w-full flex items-center justify-between mb-4 closed" >
            <p class="faq-answer text-md mt-3 w-5/6">Right now we only cover Charlotte, NC, but if we see strong adoption we hope to expand to help drivers in other congested cities as well.</p>
            <img class="h-14 faq-icon" style="transform: rotate(180deg);" src={faqIcon} alt="FAQ Icon">
        </div>
        <hr>
    </div>

    <div class="relative z-20 w-full md:w-1/2 foldInput bg-transparent border border-gray-500 rounded-full flex">
        <input class="pr-2 flex-1 h-full bg-transparent z-20 md:placeholder:text-xl pl-4" type="text" placeholder="Email address">
        <button on:click={joinWaitlist} class="whitespace-nowrap z-50 h-full px-4 md:px-8 rounded-full light-green-border light-green-text darkest underline join-btn md:text-xl">
            Find a Parking Spot
        </button>
    </div>  
</div>




<style>
    .modal-container{
        position: fixed;
        display: flex;
        width: 100%;
        height: 100%;
        background: rgba(0, 0, 0, 0.4); /* Semi-transparent black background for the blur */
        backdrop-filter: blur(5px); /* Add a blur effect to the background */
        justify-content: center;
        align-items: center;
        z-index: 50;
    }

    .modal-container div{
        z-index: 51;
    }

    footer{
        width: 100%;
    }

    .closed{
        justify-content: flex-end;
        margin-bottom: 0.5%;
        margin-top: -4%;
    }

    .closed p{
        display: none;
    }

    .faq-icon, div.w-full.flex.items-center.justify-between.mb-4{
        transition: transform 0.3s ease;
    }

    .faq-icon{
        cursor: pointer;
    }

    .activeIcon{
        transform: rotate(180deg);
    }

    hr{
        height: 1px;
        background-color: #95D5B2;
        border: none;
    }

    .faq-title, .regular{
        font-family: 'ClashGrotesk-Regular';
    }

    .faq-answer{
        font-family: 'ClashGrotesk-Extralight';
    }

    .faq, .semibold{
        font-family: 'ClashGrotesk-SemiBold';
    }

    h4{
        font-family: 'ClashGrotesk-SemiBold';
        line-height: 0.6;
    }

    .second-top-height{
        height: 85%;
    }

    .second-bottom-height{
        height: 15%;
    }

    .zero{
        -webkit-text-stroke: 1px #40916C;
        font-size: 24rem;
        line-height: 0.75;
    }

    .inputBelowZero{
        width: 88%;
    }

    .light-green-bg{
        background: #95D5B2;
    }

    .companiesHeight{
        height: 21%;
    }

    .foldInput{
        height: 3.7rem;
    }

    .medium{
        font-family: 'ClashGrotesk-Medium';
    }

    .foldInput input{
        font-family: 'ClashGrotesk-Medium';
        color: rgb(157, 170, 188);
        font-size: 1rem;
    }

    .join-btn{
        font-family: 'ClashGrotesk-SemiBold';
    }

    .light-green-border{
        border: 1px #95D5B2 solid;
    }

    .inner-cool-border{
        box-shadow: 31px 31px 31px; 
        border: 3px #95D5B2 solid; 
        filter: blur(31px);
    }

    .cool-border{
        height: 1px;
        width: 200px;
        border: 1px #95D5B2 solid;
    }

    h1{
        font-family: 'ClashGrotesk-Medium';
        font-size: 3.5rem;
        line-height: 1;
    }

    h2{
        font-size: 1.2rem;
    }

    

    @media (min-width: 768px) { 
    h1 {
        font-size: 5rem;
    }

    h2 {
        font-size: 1.5rem;
    }

    .foldInput {
        height: 3.9rem;
    }

    .foldInput input {
        font-size: 1.2rem;
    }

    .zero {
        -webkit-text-stroke: 1px #40916C;
        font-size: 5rem; /* Updated font size to be consistent */
        line-height: 0.75;
    }

    .inputBelowZero {
        width: 70%;
    }
}

@media (max-width: 900px) { 
    h1 {
        font-size: 3.3rem;
    }

    .foldInput{
        width: 90%;
    }
}


    .light-green-text{
        color: #95D5B2;
    }

    .join-link{
        text-decoration: underline;
        font-family: ClashGrotesk-Bold;
        font-size: 20px;
    }

    .header{
        font-family: ClashGrotesk-Bold;
    }

    .darkGreen{
        background: #1B4332;
    }

    .darkest{
        background: #1B152D;
        opacity: 0.8;
    }

    .fullOpacity{
        opacity: 1;
    }

    .nav{
        width: 90%;
    }

    .normal-bg{
        background: #40916C;
    }

    .normal-text{
        color: #40916C;
    }
</style>