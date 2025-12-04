<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <title>ระบบวัดประเมินผลนักเรียน</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        body { font-family: 'Sarabun', Arial, sans-serif; margin: 20px; background: #eef1f5; }
        h1, h2 { color: #2c3e50; }
        .container { max-width: 900px; margin: 0 auto; background: #fff; padding: 24px; border-radius: 10px; box-shadow: 0 2px 10px #ddd;}
        .form-section { margin-bottom: 32px; }
        label { font-weight: bold; display: block; margin-bottom: 6px;}
        input, select, textarea { width: 100%; padding: 8px; margin-bottom: 12px; border-radius: 4px; border: 1px solid #ccc;}
        table { width: 100%; border-collapse: collapse; margin-bottom: 16px;}
        th, td { border: 1px solid #aaa; padding: 8px; text-align: left; }
        th { background: #f5f7fa; }
        .btn { background: #3498db; color: #fff; padding: 8px 14px; border-radius: 5px; border: none; cursor: pointer;}
        .btn:hover { background: #217dbb; }
        .summary-section { background: #f3fafc; padding: 20px; border-radius: 8px; box-shadow: 0 1px 4px #eee;}
    </style>
</head>
<body>
    <div class="container">
        <h1>ระบบวัดประเมินผลนักเรียน</h1>

        <div class="form-section">
            <h2>1. เพิ่มวิชา</h2>
            <form id="addSubjectForm">
                <label for="subjectName">ชื่อวิชา:</label>
                <input type="text" id="subjectName" required>
                <button type="submit" class="btn">เพิ่มวิชา</button>
            </form>
            <table id="subjectTable">
                <thead><tr><th>รายชื่อวิชา</th></tr></thead>
                <tbody></tbody>
            </table>
        </div>

        <div class="form-section">
            <h2>2. กำหนดสมรรถนะ & ตัวชี้วัดสำหรับแต่ละวิชา</h2>
            <form id="addCompetencyForm">
                <label for="selectedSubject">เลือกวิชา:</label>
                <select id="selectedSubject" required></select>
                <label for="competencyName">สมรรถนะ:</label>
                <input type="text" id="competencyName" required>
                <label for="indicatorName">ตัวชี้วัด:</label>
                <textarea id="indicatorName" required></textarea>
                <button type="submit" class="btn">เพิ่มสมรรถนะและตัวชี้วัด</button>
            </form>
            <div id="competencyLists"></div>
        </div>

        <div class="summary-section">
            <h2>สรุปสำหรับนักเรียน</h2>
            <div id="studentSummary"></div>
        </div>
    </div>

    <script>
        const subjects = [];
        const competencies = {}; // { subject: [{ competency, indicator }] }

        document.getElementById('addSubjectForm').onsubmit = function(e) {
            e.preventDefault();
            const name = document.getElementById('subjectName').value.trim();
            if(name && !subjects.includes(name)) {
                subjects.push(name);
                updateSubjectTable();
                updateSubjectDropdown();
                updateStudentSummary();
            }
            document.getElementById('subjectName').value = '';
        };

        function updateSubjectTable() {
            const tbody = document.querySelector('#subjectTable tbody');
            tbody.innerHTML = subjects.map(subj => `<tr><td>${subj}</td></tr>`).join('');
        }

        function updateSubjectDropdown() {
            const sel = document.getElementById('selectedSubject');
            sel.innerHTML = subjects.map(subj => `<option value="${subj}">${subj}</option>`).join('');
        }

        document.getElementById('addCompetencyForm').onsubmit = function(e) {
            e.preventDefault();
            const subject = document.getElementById('selectedSubject').value;
            const competency = document.getElementById('competencyName').value.trim();
            const indicator = document.getElementById('indicatorName').value.trim();

            if(subject && competency && indicator) {
                if(!competencies[subject]) competencies[subject] = [];
                competencies[subject].push({ competency, indicator });
                updateCompetencyLists();
                updateStudentSummary();
            }
            document.getElementById('competencyName').value = '';
            document.getElementById('indicatorName').value = '';
        };

        function updateCompetencyLists() {
            const div = document.getElementById('competencyLists');
            div.innerHTML = '';
            Object.keys(competencies).forEach(subject => {
                div.innerHTML += `<h3>${subject}</h3>
                    <table>
                        <thead>
                            <tr><th>สมรรถนะ</th><th>ตัวชี้วัด</th></tr>
                        </thead>
                        <tbody>
                            ${competencies[subject].map(item =>
                                `<tr><td>${item.competency}</td><td>${item.indicator.replace(/\n/g,'<br>')}</td></tr>`
                            ).join('')}
                        </tbody>
                    </table>`;
            });
        }

        function updateStudentSummary() {
            const div = document.getElementById('studentSummary');
            if(subjects.length === 0) {
                div.innerHTML = "<em>ยังไม่มีข้อมูลวิชา</em>";
                return;
            }
            let html = '';
            subjects.forEach(subject => {
                html += `<h3>${subject}</h3>`;
                if (competencies[subject] && competencies[subject].length > 0) {
                    html += '<ul>';
                    competencies[subject].forEach(({competency, indicator}) => {
                        html += `<li>
                            <strong>สมรรถนะ:</strong> ${competency} <br>
                            <strong>ตัวชี้วัด:</strong> ${indicator.replace(/\n/g, '<br>')}
                        </li>`;
                    });
                    html += '</ul>';
                } else {
                    html += '<div style="color:#888;">ยังไม่มีสมรรถนะและตัวชี้วัดที่กำหนด</div>';
                }
            });
            div.innerHTML = html;
        }
    </script>
</body>
</html>
