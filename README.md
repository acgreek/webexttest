manifest.json
{

  "manifest_version": 2,
  "name": "ThePirateBayFilter",
  "version": "1.0",

  "description": "Filters old and low seed search results",

  "icons": {
    "48": "icons/border-48.png"
  },

  "content_scripts": [
    {
      "matches": ["*://thepiratebay.org/*"],
      "js": ["jquery.js", "thepiratebayfilter.js"]
    }
  ]

}

thepiratebayfilter.js
document.body.style.border = "5px solid blue";

var DaysInThePast = 3;

function toTwoCharNum(f) {
        if (f < 10)  {
                return '0' + f;
        }
        return '' + f;
}

var prevdays = [];
var i;

for (i = 0; i < DaysInThePast ; i++) {
        var prevday = new Date(Date.now() - 1000*60*60*24 * i);
        prevdays[i] = toTwoCharNum( prevday.getMonth()+1) + '.' +  toTwoCharNum( prevday.getDate()) + "&nbsp;\\d\\d:\\d\\d";
}
String.prototype.replaceAll = function(search, replacement) {
            var target = this;
               return target.replace(new RegExp(search, 'gm'), replacement);
};

$('body').each(function(){
    var pattern = /(Uploaded (Today|Y-day))/g;
    var before = '<span style="background: yellow;">';
    var before1 = '<span style="background: yellow;">';
    var after = '</span>';
    var content = $(this).html();
    content = content.replace(pattern, before+"$1"+after);
    var j;
    for (j = 0; j < DaysInThePast; j++) {
        var tp = "(Uploaded " + prevdays[j] + ")";
        content = content.replace(new RegExp(tp, "g"), before+"$1"+after);
    }
    //var remove_pattern = /<tr>.*<td class=.*<td align=\"right\">0</td>.*<td align=\"right\">0</td>\n.*</tr>/g;
    var remove_pattern = "<tr[.\n]*</tr>";

    content = content.replaceAll(remove_pattern, "");

    $(this).html(content);
});

