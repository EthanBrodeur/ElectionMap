<html lang="en">
<head>
    <meta charset="utf-8">
    <title>D3 Presidential Map</title>
    <script src="https://d3js.org/d3.v4.js"></script>
    <style>           
    div.tooltip {   
        position: absolute;         
        text-align: center;         
        width: 220px;                    
        height: 150px;                   
        padding: 2px;               
        font: 16px sans-serif;      
        background: white;    
        border: 1px;   
        stroke: black;     
        border-radius: 8px;         
        pointer-events: none;           
    }
    svg {
        display:block;
        margin:auto;
    }
</style>
</head>
<body>
    <script type="text/javascript">

        var yearString = "data/election-results-2016.csv";
        var margin = {top: 20, right: 30, bottom: 30, left: 40},
        width = 1100 - margin.left - margin.right,
        height = 800 - margin.top - margin.bottom;

        var canvas = d3.select("body")
        .append("svg")
        .attr("width", width)
        .attr("height", height);

        var tooltip = d3.select("body").append("div")   
        .attr("class", "tooltip")               
        .style("opacity", 0);

        canvas.append("text")
        .attr("class", "chart-title")
        .attr("x", width/2)
        .attr("y",height*.25)
        .text(yearString.substr(22,4) + " Presidential Election")
        .attr("font-size", "35px")
        .attr("text-anchor", "middle");


        //var years=[];
        //var parties =[];
        var stateMap = {
            "AK": [0,0],
            "ME": [0,11],
            "VT": [1,10],
            "NH": [1,11],
            "WA": [2,1],
            "ID": [2,2],
            "MT": [2,3],
            "ND": [2,4],
            "MN": [2,5],
            "IL": [2,6],
            "WI": [2,7],
            "MI": [2,8],
            "NY": [2,9],
            "RI": [2,10],
            "MA": [2,11],
            "OR": [3,1],
            "NV": [3,2],
            "WY": [3,3],
            "SD": [3,4],
            "IA": [3,5],
            "IN": [3,6],
            "OH": [3,7],
            "PA": [3,8],
            "NJ": [3,9],
            "CT": [3,10],
            "CA": [4,1],
            "UT": [4,2],
            "CO": [4,3],
            "NE": [4,4],
            "MO": [4,5],
            "KY": [4,6],
            "WV": [4,7],
            "VA": [4,8],
            "MD": [4,9],
            "DC": [4,10],
            "AZ": [5,2],
            "NM": [5,3],
            "KS": [5,4],
            "AR": [5,5],
            "TN": [5,6],
            "NC": [5,7],
            "SC": [5,8],
            "DE": [5,9],
            "OK": [6,4],
            "LA": [6,5],
            "MS": [6,6],
            "AL": [6,7],
            "GA": [6,8],
            "HI": [7,1],
            "TX": [7,4],
            "FL": [7,9]
        };

        d3.csv("data/yearwise-winner.csv", function(data){

            var maxX = d3.max(data, function(d) { return d.YEAR});
            var minX = d3.min(data, function(d) { return d.YEAR});
            // console.log(maxX);
            //var maxX = d3.max(winners[0]);
            //var minX = d3.min(winners[0]);

            function color(d) {
                if (d == "D"){
                    return d3.color("steelblue"); 
                }
                if (d == "R"){
                    return d3.color("indianred");
                }
            }

            var x = d3.scaleLinear()
            .domain([minX, maxX])
            .range([30, width-30]);

            var circles = canvas.selectAll("circle")
            .data(data)
            .enter()
            .append("circle")
            .attr("cx", function(d) {
                // console.log();
                return (x(d.YEAR));
            })
            .attr("cy", function(d) {
                return (height*.1);
            })
            .attr("r", 15)
            .attr("fill", function(d) {
                return (color(d.PARTY));
            }).on(
            "mouseover", function(d) {
                // console.log("year was",d.YEAR);
                d3.select(this)
                .attr("fill", "yellow")
                .attr("r", 30);
            })
            .on(
                "click", function(d) {
                    yearString = "data/election-results-"+d.YEAR+".csv";
                //console.log(yearString);
                canvas.selectAll("g").remove();
                loadMap();
                //console.log("reached");
            })
            .on(
                "mouseout", function(d) {
                    d3.select(this)
                    .attr("fill", function(d) {return (color(d.PARTY))})
                    .attr("r",15);
                });

            var circleLabels = canvas.selectAll("text.circle-labels")
            .data(data)
            .enter()
            .append("text")
            .attr("class","circle-labels")
            .attr("x", function(d) {
                return (x(d.YEAR));
            })
            .attr("y", function(d) {
                return (height*.17);
            })
            .text(function(d) { return d.YEAR;})
            .attr("font-family", "sans-serif")
            .attr("font-size", "20px")
            .attr("fill", function(d){
                return (color(d.PARTY));
            })
            .attr("text-anchor", "middle");


        });
        var dummyD = 50;
        let dummyR = 0;
        let rw = width*.0835;
        let rh = height*.02;
        for (let i = 0; i < 12; i++) {
            canvas.append("rect")
            .attr("x", rw*i)
            .attr("y", height*.96)
            .attr("width", rw)
            .attr("height", rh)
            .attr("fill", stateColor(0, dummyD, dummyR));
            canvas.append("text")
            .attr("x", rw*i + rw/2)
            .attr("y", height)
            .attr("text-anchor", "middle")
            .text(function(){
                if (dummyD == 50) {
                    return "50+";
                }
                if (dummyD == -60) {
                    return "< -50";
                }
                return (dummyD+10) + " to " + (dummyD);
            });
            dummyD -= 10;

        }

        var xStatePos = d3.scaleLinear()
        .domain([0,11])
        .range([width*.1,width*.9]);

        var yStatePos = d3.scaleLinear()
        .domain([0,7])
        .range([height*.25, height*.9]);

        let rectWidth = width*.07;
        let rectHeight = height*.09;

        function stateColor(d, demp, repp){
            if (demp == repp) {
                if (d.R_Votes > d.D_Votes){
                    return d3.rgb("#ffcccc");
                }
                else{
                    return d3.rgb("#66a3ff");
                }
            }
            if (demp - repp >= 50.0){
                return d3.rgb("#001a33");
            }
            else if (demp - repp >= 40.0){
                return d3.rgb("#00264d");
            }
            else if (demp - repp >= 30.0){
                return d3.rgb("#003366");
            }
            else if (demp - repp >= 20.0){
                return d3.rgb("#005ce6");
            }
            else if (demp - repp >= 10.0){
                return d3.rgb("#1a75ff");
            }
            else if (demp - repp >= 0.0){
                return d3.rgb("#66a3ff");
            }
            else if (demp - repp >= -10.0){
                return d3.rgb("#ffcccc");
            }
            else if (demp - repp >= -20.0){
                return d3.rgb("#ff8080");
            }
            else if (demp - repp >= -30.0){
                return d3.rgb("#ff4d4d");
            }
            else if (demp - repp >= -40.0){
                return d3.rgb("#ff0000");
                //return d3.rgb("#cc0000");
            }
            else if (demp - repp >= -50.0){
                //return d3.rgb("#cc0000");
                return d3.rgb("#800000");

            }
            else {
                return d3.rgb("#3f0000");
            }
        }
        function loadMap() {
            canvas.select("text.chart-title").text(yearString.substr(22,4) + " Presidential Election");
            d3.csv(yearString, function(data){
                //console.log(yearString);
                //console.log(data);
                var rects = canvas.append("g").selectAll("rect")
                .data(data);
                rects.exit().remove();
                rects
                .enter()
                .append("rect")
                .attr("width", rectWidth)
                .attr("height", rectHeight)
                .attr("x", function(d){
                    if (!(d.Abbreviation in stateMap)){
                        //console.log(d.Abbreviation);
                    }
                    return xStatePos(stateMap[d.Abbreviation][1]) - rectWidth/2;
                })
                .attr("y", function(d){
                    return yStatePos(stateMap[d.Abbreviation][0]) - rectHeight/2    ;
                })
                .attr("fill", function(d) { return stateColor(d,d.D_Percentage,d.R_Percentage)})
                .on("mouseover", function (d) {drawToolTip(d)})                  
                .on("mouseout", function (d) {clearToolTip(d)});

                var rectText = canvas.append("g").selectAll("text.state-labels")
                .data(data);
                rectText.exit().remove();
                rectText
                .enter()
                .append("text")
                .attr("class", "state-labels")
                .text(function(d) { return d.Abbreviation + "\n" + d.Total_EV;})
                .attr("fill", "black")
                .attr("font-family", "sans-serif")
                .attr("font-size", "20px")
                .attr("x", function (d){
                    return xStatePos(stateMap[d.Abbreviation][1]);
                })
                .attr("y", function (d){ return yStatePos(stateMap[d.Abbreviation][0]);})
                .attr("text-anchor", "middle")
                .on("mouseover", function(d) {drawToolTip(d)})                  
                .on("mouseout", function(d) {clearToolTip(d)});

            });
        }
        function initMap() {

            d3.csv(yearString, function(data){
                //console.log(yearString);
                //console.log(data);
                var rects = canvas.append("g").selectAll("rect")
                .data(data)
                .enter()
                .append("rect")
                .attr("width", rectWidth)
                .attr("height", rectHeight)
                .attr("x", function(d){
                    return xStatePos(stateMap[d.Abbreviation][1]) - rectWidth/2;
                })
                .attr("y", function(d){
                    return yStatePos(stateMap[d.Abbreviation][0]) - rectHeight/2    ;
                })
                .attr("fill", function(d) { return stateColor(d,d.D_Percentage,d.R_Percentage)})
                .on("mouseover", function (d) {drawToolTip(d)})                  
                .on("mouseout", function (d) {clearToolTip(d)});

                var rectText = canvas.append("g").selectAll("text.state-labels")
                .data(data)
                .enter()
                .append("text")
                .attr("class", "state-labels")
                .text(function(d) { return d.Abbreviation + "\n" + d.Total_EV;})
                .attr("fill", "black")
                .attr("font-family", "sans-serif")
                .attr("font-size", "20px")
                .attr("x", function (d){
                    return xStatePos(stateMap[d.Abbreviation][1]);
                })
                .attr("y", function (d){ return yStatePos(stateMap[d.Abbreviation][0]);})
                .attr("text-anchor", "middle")
                .on("mouseover", function (d) {drawToolTip(d)})                  
                .on("mouseout", function (d) {clearToolTip(d)});

            });
        }
        function drawToolTip(d) {
            var winnerString = "";
            var loserString = "";
            if (parseFloat(d.D_Votes) > parseFloat(d.R_Votes)) {
                winnerString = " - Democrat " + d.D_Nominee + " " + d.D_Votes + " (" + d.D_Percentage+"%)";
                loserString = " - Republican " + d.R_Nominee + " " +d.R_Votes + " (" + d.R_Percentage+"%)";
            }
            else {
                winnerString = " - Republican " + d.R_Nominee + " " +d.R_Votes + " (" + d.R_Percentage+"%)";
                loserString = " - Democrat " + d.D_Nominee + " " +d.D_Votes + " (" + d.D_Percentage+"%)";

            }
            tooltip.html(d.State + "\n" +"- Electoral Votes: " +  d.Total_EV + "\n" + winnerString + "\n" + loserString)
            .style("left", (d3.event.pageX) + "px")     
            .style("top", (d3.event.pageY ) + "px")
            .style("opacity", .9);  
        }
        function clearToolTip(d) {
            tooltip.style("opacity", 0);    

        }
        initMap();
    </script>
</body>
</html>

