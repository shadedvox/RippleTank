# Ripple Tank

A tiny web app that turns clicks into water ripples with sound. It is a single HTML file under 3 KB, with no libraries and no external files.

## How to use

1. Open the file in a browser or paste this one-liner into the address bar.
```html
data:text/html,<style>body{margin:0;background:black}canvas{width:100vw;height:100vh;display:block;touch-action:none}</style><canvas id=rc></canvas><script>const ctx=rc.getContext('2d'),h=200,R=4,srcs=[];let w,img,down,ac;function resize(){w=Math.round(h*innerWidth/innerHeight);rc.width=w;rc.height=h;img=ctx.createImageData(w,h)}resize();onresize=resize;rc.onpointerdown=e=>{down={time:performance.now(),x:e.clientX,y:e.clientY}};rc.onpointerup=()=>{const p=down;down=null;if(!p||srcs.length>=8)return;const ht=Math.min((performance.now()-p.time)/1000,3);ac=ac||new AudioContext();if(ac.state==='suspended')ac.resume();const wd=0.3/(1+ht*2),osc=ac.createOscillator(),gain=ac.createGain();osc.frequency.value=120*2**(wd*8);gain.gain.value=0;osc.connect(gain).connect(ac.destination);osc.start();srcs.push({x:p.x/innerWidth*w,y:p.y/innerHeight*h,wd,sp:0.02+ht*0.03,rad:10-ht*3,birth:performance.now(),tol:(10+ht*3.33333)*1000,ampT:0,osc,gain})};function frame(t){for(let i=srcs.length-1;i>=0;i--){const s=srcs[i],age=t-s.birth;s.ampT=Math.exp(-5/(s.tol/2)*Math.max(0,age-s.tol/2));if(s.ampT<0.01){s.osc.stop();s.osc.disconnect();s.gain.disconnect();srcs.splice(i,1);continue}s.gain.gain.setTargetAtTime(s.ampT*0.05,ac.currentTime,0.05)}for(let i=0;i<w*h;i++){const y=Math.floor(i/w),x=i-y*w;let v=0,glow=0;for(const s of srcs){const d=Math.hypot(x-s.x,y-s.y);glow=Math.max(glow,Math.exp(-d*d/(2*R*R))*s.ampT);v+=s.ampT*Math.exp(-d*s.rad/1500)*Math.sin(s.wd*(d-t*s.sp))}v/=Math.sqrt(Math.max(1,srcs.length));const b=50+127*v,c=b+(255-b)*glow;img.data.set([255*glow,c,c,255],i*4)}ctx.putImageData(img,0,0);requestAnimationFrame(frame)}requestAnimationFrame(frame)</script>
```
2. Click or tap anywhere to drop a ripple.
3. Hold the click longer for wider, faster, lower-pitched ripples.
4. Drop several ripples and watch them interfere with each other.

## How it works

- **Waves:** every pixel adds up one sine wave per source. Where crests meet, it gets bright. Where a crest meets a trough, they cancel.
- **Sound:** each source plays a tone using the Web Audio API. Wider rings sound lower and tighter rings sound higher.
- **Fading:** waves weaken with distance. Each source holds full strength until half of its life has passed, then fades out exponentially and is removed.
- **Canvas:** the picture is drawn on a small canvas that matches the window's shape, then scaled up by CSS to fill the screen.

## Limits

- Up to 8 ripples at a time. A new one can be added once an old one has faded.
- Sound starts after the first click, because browsers block audio until you interact with the page.

## Tweaking

- `h` is the canvas height in pixels. Raise it for sharper rings, lower it if it lags.
- `0.05` in the gain line is the volume per ripple.
- The hold time is capped at 3 seconds.