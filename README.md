# AdventOfCode-2017
My solutions to the 2017 [Advent of Code](http://adventofcode.com/2017) challenges, dumped into one list with explanations.

Javascript solutions are executed in the browser console of the webpage that opens when you ask Advent of Code for your puzzle input.
05AB1E solutions are executed in tio.run, with the input being pasted there.

## Day 1
### Part 1
Javascript:
`document.body.innerText.replace(/[^\d]+/g,'').split('').filter((d,i,a)=>d==a[(i+1)%a.length]).reduce((t,v)=>t+parseInt(v),0)`
05AB1E:
`1ôDÀ->ΘÏ1ôO`

Takes the text from the page, keeps only the numbers, splits into an array, keeps only the items where the next one in the list (wrapping from end to front if needed) is the same, and sums what remains.
### Part 2
`document.body.innerText.replace(/[^\d]+/g,'').split('').filter((d,i,a)=>d==a[(i+a.length/2)%a.length]).reduce((t,v)=>t+parseInt(v),0)`

Same trick, but look ahead by half of the length of the list, rather than looking one element ahead.

## Day 2
### Part 1
Javascript:
`document.body.innerText.split('\n').filter(l=>!!l).map(line=>line.split(/\s+/g).reduce((t,v)=>{t.max=Math.max(t.max,v);t.min=Math.min(t.min,v);return t;},{min:Number.POSITIVE_INFINITY,max:Number.NEGATIVE_INFINITY})).map(t=>t.max-t.min).reduce((t,v)=>t+v,0)`
Turn the input into a 2D array (a matrix). Then transform each row into a {min, max} pair, transform that into max-min, and finally sum all of these values.

### Part 2
Javascript:
`document.body.innerText.split('\n').filter(l=>!!l).map(line=>line.trim().split(/\s+/g).map(v=>parseInt(v))).map(l=>l.sort((a,b)=>b-a)).map(l=>{ let d=0.5; for(let i=0;i<l.length-1&&Math.floor(d)!==d;i++){ for(let j=i+1;j<l.length&&Math.floor(d)!==d;j++) { d=l[i]/l[j]; } } return d; }).reduce((t,v)=>t+v,0);`
Turn the input into a 2D array (a matrix). Clean it up and turn each row into a sorted list of numbers. On each row, iterate from largest to smallest. Divide that number by each of the later numbers in the list. Repeat this until an even division is discovered. Now that each "row" is just the first even division result, sum them up.


## Day 3
### Part 1
Javascript:
```
manhattan=(target)=>{
  // The pattern goes: right once, up once, left twice, down twice, right three times, up three times, left four times, down four times... and so on
  // We can do some clever math with this, leveraging modular arithmetic and summation formulae.
  // First, we take the input minus one. This is how many cells to spiral. Next, we compute how far in each direction it would have to go with the above observations.
  // Then, we let left/right and up/down motion cancel out, add the absolute value of each direction, and that is our manhattan distance!
  // But before all that, let's cheat and count the slow way.
  let cell=1;step=1,dir=0;dist=[0,0,0,0]; // [right, up, left, down]
  while(cell < target) {
    let diff = Math.min(step, target-cell);
    dist[dir] += diff;
    cell += diff;
    diff = Math.min(step, target-cell);
    cell += diff;
    dir = (dir+1)%4;
    dist[dir]+=diff;
    dir = (dir+1)%4;
    step += 1;
  }
  return Math.abs(dist[0]-dist[2])+Math.abs(dist[1]-dist[3]);
};
console.log(manhattan(INPUT_NUMBER_HERE));
```

### Part 2:
Javascript:
```
spiralFill=(target)=>{
  // The pattern goes: right once, up once, left twice, down twice, right three times, up three times, left four times, down four times... and so on
  // We can do some clever math with this, leveraging modular arithmetic and summation formulae. 
  // Each cell is the sum of its neighbors, locking once set, starting from 1 and spiraling outwards as described above.
  // The neighbors include diagonals. As such, a cell can see at most four values: the one computed before it, and up to three earlier values.
  // To make searching and storage easier, we'll keep a linear array where each "cell" knows its coordinates.
  // The final output is the first cell larger than the target value when doing this.
  // There is surely a non-brute-force method that leverages clever math based on the above observations...
  let cells=[], stepSize=1, value=1, x=0, y=0, dir=0, step=1;
  while(value<=target) {
    cells.push({value,x,y});
    x += dir%2===0? [1,-1][dir/2] : 0;
    y += (dir-1)%2===0? [-1,1][(dir-1)/2] : 0;
    step--;
    if (step === 0) {
      dir = (dir+1)%4;
      if(dir===2||dir===0) stepSize++;
      step=stepSize;
    }
    let coords = [ [x-1, y], [x-1, y-1], [x, y-1], [x+1, y-1], [x+1, y], [x+1, y+1], [x, y+1], [x-1, y+1] ];
    value = cells.filter(cell => coords.find(coord => cell.x==coord[0]&&cell.y==coord[1])).reduce((t,v)=>t+v.value,0);
  }
  return value;
};
console.log(spiralFill(INPUT_NUMBER_HERE));
```

## Day 4

### Part 1
Javascript
`document.body.innerText.trim().split('\n').map(l=>l.trim().split(' ')).filter(phrase => phrase.map((w,i) => phrase.lastIndexOf(w)==i).reduce((t,v)=>t&&v,true)).length;`
Split into lines, split lines into words, transform into a true/false value (false if any word occurs twice in the line), return how many are true.

### Part 2
Javascript
`document.body.innerText.trim().split('\n').map(l=>l.trim().split(' ')).filter(phrase => phrase.map(word=>word.split('').sort().join('')).map((w,i,a) => a.lastIndexOf(w)==i).reduce((t,v)=>t&&v,true)).length`
Same deal, but also alphabetically sort each word in each phrase before checking for duplicates.

## Day 5
### Part 1
Javascript
`jumps=document.body.innerText.trim().split('\n').map(Number);index=0;steps=0;while(index<jumps.length&&index>=0) {jumps[index]++;index += jumps[index]-1;steps++;}console.log(steps);`
Pretty self-explanatory. Direct implementation of the requirements.

### Part 2
Javascript
`jumps=document.body.innerText.trim().split('\n').map(Number);index=0;steps=0;while(index<jumps.length&&index>=0) {m=jumps[index];jumps[index]=m<3?m+1:m-1;index+=m;steps++;}console.log(steps);`
Same trick, but with the extra logic for increment/decrement in it.

## Day 6
### Part 1
Javascript (set input number variable, since there was not a dedicated page for input this day)
```
let layouts = [['your','input','numbers','here']];
let steps = 0;

let noDuplicates = layout => {
  for(let r = 0; r < layouts.length; r++) {
    let dup = true;
    for(let c = 0; c < layouts[r].length; c++) {
      dup = dup && layout[c] === layouts[r][c];
    }
    if (dup) return false;
  }
  return true;
  // Copying arrays like this works but is too slow on large inputs. Thus we do it the old fashioned way.
  //let noDuplicates = layout=>!layouts.map(c=>c.map((v,i)=>v===layout[i]).reduce((t,v)=>t&&v)).reduce((t,v)=>t&&v);
}

while(true) {
  let next=Array.apply(Array,layouts[layouts.length-1]);
  let m=Math.max.apply(null, next);
  let i=next.indexOf(m);
  next[i]=0;
  while(m>0) { next[(++i)%next.length]++; m--; }
  steps++;
  if(noDuplicates(next)) { layouts.push(next); }
  else { console.log(`GOT IT, it took ${steps} steps to find a cycle.`); break; }
  if(steps > 100000) {console.log('FAILED TO FIND A CYCLE AFTER 100,000 ITERATIONS, QUITTING.');break;}
}
console.log(steps);
```

### Part 2
Javascript (same thing)
```
let layouts = [['your','input','values','here']];
let steps = 0;

let hasDuplicates = layout => {
  for(let r = 0; r < layouts.length; r++) {
    let dup = true;
    for(let c = 0; c < layouts[r].length; c++) {
      dup = dup && layout[c] === layouts[r][c];
    }
    if (dup) return r;
  }
  return false;
  // Copying arrays like this works but is too slow on large inputs. Thus we do it the old fashioned way.
  //let noDuplicates = layout=>!layouts.map(c=>c.map((v,i)=>v===layout[i]).reduce((t,v)=>t&&v)).reduce((t,v)=>t&&v);
}

while(true) {
  let next=Array.apply(Array,layouts[layouts.length-1]);
  let m=Math.max.apply(null, next);
  let i=next.indexOf(m);
  next[i]=0;
  while(m>0) { next[(++i)%next.length]++; m--; }
  steps++;
  let iter = hasDuplicates(next);
  if(iter === false) { layouts.push(next); }
  else { console.log(`Found a duplicate of row ${layouts.length} on row ${iter}, so the cycle has length ${layouts.length-iter}.`); break; }
  if(steps > 100000) {console.log('FAILED TO FIND A CYCLE AFTER 100,000 ITERATIONS, QUITTING.');break;}
}
console.log(steps);
```

## Day 7
### Part 1
Javascript
1all=document.body.innerText.trim().split('\n') .map(t => /^([a-z]+)\s\(\d+\)( -> ([a-z, ]+))?$/.exec(t.trim())) .map(l => [l[1],...(l[3]||"").split(', ')]); held = all.reduce((t,v) => [...t,...v.slice(1)], []).sort().filter((n,i,a)=>!!n&&a.lastIndexOf(n)===i); console.log(all.filter(r=>held.indexOf(r[0])===-1).map(root=>root[0]));`
### Part 2
Javascript
```
let tree = {};

document.body.innerText.trim().split('\n')
  .map(t => /^([a-z]+)\s\((\d+)\)( -> ([a-z, ]+))?$/.exec(t.trim()))
  .map(l => [l[1],Number(l[2]),...(l[4]||"").split(', ')])
  .forEach(n => tree[n[0]] = { weight:n[1],children:n.slice(2).filter(c=>!!c) });

let keys = Object.keys(tree);
let topKey = keys.filter(k => keys.filter(j => tree[j].children.indexOf(k) !== -1).length === 0)[0];
compose(topKey);
findImbalance();

function compose(k) {
  if (tree[k] && tree[k].children.length > 0) {
    tree[k].children.forEach(compose);
  }

  tree[k].sum = tree[k].weight + tree[k].children.map(j => tree[j].sum).reduce((t,v)=>t+v,0);
  tree[k].balanced = [...new Set(tree[k].children.map(j => tree[j].sum))].length <= 1;
  tree[k].output = `${tree[k].balanced?'\u2713':'\u2717'} ${k} @ ${tree[k].weight} -> ${tree[k].sum}`;
  let childOutputs = tree[k].children.map(j => tree[j].output.split('\n').map(l=>`  ${l}`).join('\n')).join('\n');
  if (childOutputs) {
    tree[k].output += '\n' + childOutputs;
  }
}

function findImbalance() {
  let inspect = topKey;
  while (!tree[inspect].balanced && tree[inspect].children.some(c=>!tree[c].balanced)) {
    inspect = tree[inspect].children.filter(c=>!tree[c].balanced)[0];
  }

  let goodValue = tree[inspect].children.map(c=>tree[c].sum).filter((v,i,a) => a.indexOf(v)!==a.lastIndexOf(v))[0];
  let badValue  = tree[inspect].children.map(c=>tree[c].sum).filter((v,i,a) => a.indexOf(v)===a.lastIndexOf(v))[0];
  let weight = tree[inspect].children.map(c=>tree[c]).filter(n=>n.sum===badValue)[0].weight;
  console.log(weight + (goodValue-badValue));
}
```

## Day 8
### Part 1
Javascript
```
let registers=document.body.innerText.trim().split('\n').reduce((t,v) => {
  let [,reg,incdec,delta,check,cmp,test] = /^([a-zA-Z]+) (inc|dec) ([-\d]+) if ([a-zA-Z]+) ([!<>=]+) ([-\d]+)$/.exec(v.trim());
  let cv = Number(t[check]||0), tv=Number(test);
  if(incdec === 'dec') delta = 0-Number(delta);
  if ( (cv>tv&&cmp=='>')||(cv<tv&&cmp=='<')||(cv==tv&&cmp=='==')||(cv!=tv&&cmp=='!=')||(cv<=tv&&cmp=='<=')||(cv>=tv&&cmp=='>=')) { t[reg]=Number(t[reg]||0)+Number(delta); }
  return t;
}, {});
Object.keys(registers).reduce((max,key)=>Math.max(max,registers[key]),Number.NEGATIVE_INFINITY)
```
Almost verbatim implementation of the requirements to calculate the value of every register, then a reduce to find the largest one's value.
### Part 2
Javascript
```
document.body.innerText.trim().split('\n').reduce((t,v) => {
  let [,reg,incdec,delta,check,cmp,test] = /^([a-zA-Z]+) (inc|dec) ([-\d]+) if ([a-zA-Z]+) ([!<>=]+) ([-\d]+)$/.exec(v.trim());
  let cv = Number(t[check]||0), tv=Number(test);
  if(incdec === 'dec') delta = 0-Number(delta);
  if ( (cv>tv&&cmp=='>')||(cv<tv&&cmp=='<')||(cv==tv&&cmp=='==')||(cv!=tv&&cmp=='!=')||(cv<=tv&&cmp=='<=')||(cv>=tv&&cmp=='>=')) { t[reg]=Number(t[reg]||0)+Number(delta); if (t[reg] > t._max) {t._max=t[reg]}; }
  return t;
}, {_max:Number.NEGATIVE_INFINITY})._max
```

## Day 9
### Part 1
Javascript
`document.body.innerText.trim().replace(/!./g,'').replace(/<[^>]*>/g,'').replace(/,/g,'').split('').reduce((t,v) => ({n:t.n+(v=='{'?1:-1),a:t.a+(v=='{'?0:t.n)}), {n:0,a:0}).a`
Remove the negated characters, remove the garbage, remove the commas, and start processing the string that is now composed entirely of `{` and `}`, from left to right. At each `{`, increment the nesting counter (starting at 0). At each `}`, add the nesting counter to the total (starting at 0) and then decrement the nesting counter.
### Part 2
`document.body.innerText.trim().replace(/!./g,'').replace(/<[^>]*>/g,g=>g.length-2).split(/[^\d]+/).filter(d=>!!d).reduce((t,v)=>t+Number(v),0)`
Much simpler: remove the negated characters, replace the garbage with its length (not counting the `<` and `>`), sum the numbers in what remains.

## Day 10
###Part 1
Javascript
```
input=document.body.innerText.trim().split(',').map(Number);
c=0; s=0; ring=Array(256).fill(0).map((v,i)=>i);
while(input.length>0) {
  let l=input.shift();
  let bigRing = [...ring,...ring,...ring];
  let replacement = bigRing.slice(c,c+l).reverse();
  bigRing.splice(c,l,...replacement);
  bigRing.splice(c+256,l,...replacement);
  ring=bigRing.slice(256,512);
  c=(c+l+s)%256;
  s++;
}
console.log(ring[0]*ring[1]);
```
Make a ring [0,...,255]. Then, when doing the steps of the challenge, operate on a triplicated version of that ring. This makes wrapping easier. Taking the middle of the triplicate restores the right ring.
### Part 2
Javascript
```
input=document.body.innerText.trim().split('').map(c=>c.charCodeAt(0)).concat([17, 31, 73, 47, 23]);
c=0; s=0; ring=Array(256).fill(0).map((v,i)=>i);
round=input=>{
while(input.length>0) {
  let l=input.shift();
  let bigRing = [...ring,...ring,...ring];
  let replacement = bigRing.slice(c,c+l).reverse();
  bigRing.splice(c,l,...replacement);
  bigRing.splice(c+256,l,...replacement);
  ring=bigRing.slice(256,512);
  c=(c+l+s)%256;
  s++;
}
};
for(let r=0;r<64;r++) { round(input.slice(0)); }
dense = '';
for(let x=0;x<16;x++) { dense += ('0'+ring.slice(x*16,x*16+16).reduce((t,v)=>t^Number(v),0x00).toString(16)).slice(-2); }
console.log(dense);
```
First, transform the input into a sequence of character codes and tack on the predefined values from the challenge.
Then, run 256 rounds of part 1's algorithm to get the final ring.
Then take each group of 16 values, xor them together, and concatenate their two-digit hexadecimal representations.

## Day 11
### Part 1
`steps = document.body.innerText.trim().split(',').reduce((t,v)=>{t[v]++;return t;}, {n:0,ne:0,se:0,s:0,sw:0,nw:0}); dirs=['n','ne','se','s','sw','nw']; oppose = {n_s:Math.min(steps.n,steps.s), ne_sw:Math.min(steps.ne,steps.sw), nw_se:Math.min(steps.nw,steps.se)}; steps.n -= oppose.n_s; steps.s -= oppose.n_s; steps.ne -= oppose.ne_sw; steps.sw -= oppose.ne_sw; steps.nw -= oppose.nw_se; steps.se -= oppose.nw_se; middle=dirs.indexOf(dirs.filter((k,i,a)=>steps[k]>0&&steps[a[(i+5)%6]]>0&&steps[a[(i+1)%6]]>0)[0]); diff = Math.min(steps[dirs[(middle+5)%6]],steps[dirs[(middle+1)%6]]); steps[dirs[(middle+5)%6]]-=diff; steps[dirs[(middle+1)%6]]-=diff; steps[dirs[middle]]+=diff; console.log(dirs.reduce((t,k)=>t+steps[k],0));`
### Part 2
```
route = document.body.innerText.trim().split(',');
dirs=['n','ne','se','s','sw','nw'];

simplify=(given) => { let steps=Object.assign({},given); let oppose = {n_s:Math.min(steps.n,steps.s), ne_sw:Math.min(steps.ne,steps.sw), nw_se:Math.min(steps.nw,steps.se)}; steps.n -= oppose.n_s; steps.s -= oppose.n_s; steps.ne -= oppose.ne_sw; steps.sw -= oppose.ne_sw; steps.nw -= oppose.nw_se; steps.se -= oppose.nw_se; let middle=dirs.indexOf(dirs.filter((k,i,a)=>steps[k]>0&&steps[a[(i+5)%6]]>0&&steps[a[(i+1)%6]]>0)[0]); if(middle>0) {let diff = Math.min(steps[dirs[(middle+5)%6]],steps[dirs[(middle+1)%6]]); steps[dirs[(middle+5)%6]]-=diff; steps[dirs[(middle+1)%6]]-=diff; steps[dirs[middle]]+=diff;} return steps; }

distance=(given) => { let steps=simplify(given); return dirs.reduce((t,k)=>t+steps[k],0); };

currentSteps = { n:0, ne:0, se:0, s:0, sw:0, nw:0 };
maxDistance = 0;
route.forEach(step => { currentSteps[step]++; currentSteps = simplify(currentSteps); maxDistance = Math.max(maxDistance, distance(currentSteps)); });
console.log(currentSteps, maxDistance);
```

## Day 12
### Part 1
Javascript
```
links=document.body.innerText.trim().split('\n').map(l=>l.match(/^\d+ <-> ([\d, ]+)$/)[1].split(/[^\d]+/).map(Number));
visited=Array(links.length).fill(false);
visit=index=>{
  let reachable = [];
  links[index].forEach(l => { reachable.push(l); if(!visited[l]) {visited[l]=true; reachable = reachable.concat(visit(l)); } });
  reachable = [...new Set(reachable)];
  links[index] = reachable;
  return reachable;
};
visit(0);
console.log(links[0].length);
```
This is a common graph traversal problem with a common solution.
### Part 2
```
links=document.body.innerText.trim().split('\n').map(l=>l.match(/^\d+ <-> ([\d, ]+)$/)[1].split(/[^\d]+/).map(Number));
visited=Array(links.length).fill(false);
visit=index=>{
  let reachable = [];
  links[index].forEach(l => { reachable.push(l); if(!visited[l]) {visited[l]=true; reachable = reachable.concat(visit(l)); } });
  reachable = [...new Set(reachable)];
  links[index] = reachable;
  return reachable;
};
let numGroups = 0;
let limit=100000;
while(visited.indexOf(false) !== -1 && --limit>0) {
  visit(visited.indexOf(false));
  numGroups++;
}
console.log(numGroups);
```
Similar trick, but making sure to visit every node and tracking how many times we have to manually restart.
