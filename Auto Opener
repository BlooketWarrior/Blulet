(async () => {
    if (!window.ac?.market?.getPacks) return alert(`This script has been patched, open an issue.\nhttps://github.com/BlacketWare/Blulet`);

    let packs = await ac.market.getPacks();
    let blues = await ac.market.getBlues();
    let rarities = await ac.rarities.getRarities();
    let me = (await ac.user.getUser()).data.user;

    let packName = prompt('Which pack would you like to open?\n\nList:\n' + packs.map(a => a.name).join('\n'));
    let pack = packs.find(p => p.name === packName);
    if (!pack) return alert('I couldn\'t find that pack...');

    let amount = prompt('How many packs do you want to open?\nEnter * for the max possible.');
    if (amount.toString() === '*') amount = Math.floor(me.tokens / pack.price);
    if (isNaN(amount) || amount < 0) return alert('Invalid amount.');
    if (amount < 1 || amount * pack.price > me.tokens) return alert('You do not have enough tokens.');

    let speed = Number.parseInt(prompt('What speed (in ms) would you like this to open at?\nOur current recommendation is around 750.'));
    if (isNaN(speed)) return alert('Invalid speed.');
    if (speed < 600) return alert('The script speed should be above 600 to avoid a large ratelimit.');

    let unlockedBlues = [];
    let i = 0;

    const buy = async () => {
        await ac.market.purchasePack(pack.name).then((data) => {
            if (data.error) return console.log(`Error opening`, data);
            i++;
            console.log('%c%s', `color: ${rarities.find(r => r.name === blues.find(b => b.name === data.blue).rarity).color}; font-size: 25px; padding: 10px 0 15px 20px;`, data.blue);
            unlockedBlues.push(data.blue);
        }).catch((err) => {
            if (err?.request?.status == 401) location.href = '/login';
            else if (err?.request.status === 429) console.log('%c%s', `color: black; font-size: 15px; padding: 10px 0 15px 20px;`, `Ratelimited!`);
            else console.log(`Unknown error. Report this on Github.`, err);
        });
    };

    let interval = setInterval(() => {
        if (i < amount) buy(pack);
        else {
            clearInterval(interval);
            let count = {};
            unlockedBlues.forEach(blue => count[blue] = (count[blue] || 0) + 1);
            alert(`Final Results:\n` + Object.entries(count).map((x) => `    ${x[1]} ${x[0]}`).join(`\n`));
        };
    }, speed);
})();
