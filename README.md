# demo-repo
This is a demo repository <br>
This is new line for first commit <br>
This is the third line for second commit <br>
This is sample text <br>
This is another sample text <br>

SELECT cu.firstname,cu.lastname,o.name,COUNT(DISTINCT lcu.idCourse) AS enrolled_courses,COUNT(DISTINCT user_reads.idCourse) AS completed_courses FROM core_user AS cu JOIN organizations AS o ON cu.orgId = o.id JOIN learning_course_user AS lcu ON lcu.userid = cu.id 

    LEFT JOIN (
        SELECT idCourse, COUNT(DISTINCT id) AS total
        FROM course_learning_lessons 
        GROUP BY idCourse
    ) AS course_totals ON course_totals.idCourse = lcu.idCourse

    LEFT JOIN (
        SELECT userid, idCourse, COUNT(DISTINCT idLesson) AS completed
        FROM course_lesson_user 
        GROUP BY userid, idCourse
    ) AS user_reads ON user_reads.idCourse = lcu.idCourse 
                AND user_reads.userid = cu.id
                AND user_reads.completed = course_totals.total

    GROUP BY cu.id

SELECT lc.courseName,lc.courseType,COUNT( DISTINCT lcu.id) AS enrolledUsers,count(DISTINCT user_reads.userid) AS completedUsers FROM learning_course AS lc LEFT JOIN learning_course_user as lcu on lcu.idCourse = lc.idCourse
    LEFT JOIN (
        SELECT idCourse, COUNT(*) AS total
        FROM course_learning_lessons 
        GROUP BY idCourse
    ) AS course_totals 
        ON course_totals.idCourse = lcu.idCourse

    LEFT JOIN (
        SELECT userid, idCourse, COUNT(*) AS completed
        FROM course_lesson_user 
        GROUP BY userid, idCourse
    ) AS user_reads 
        ON user_reads.idCourse = lcu.idCourse 
        AND user_reads.userid = lcu.userid 
        AND user_reads.completed = course_totals.total

    GROUP BY lc.idCourse

 SELECT 
    lcp.journeyName,
    COUNT(DISTINCT lcpc.idCourse) AS enrolled_courses,
    COUNT(DISTINCT lcpu.userid) AS enrolled_users,
    COUNT(DISTINCT journey_reads.userid) AS completed_users
    FROM learning_coursepath AS lcp 
    LEFT JOIN learning_coursepath_courses AS lcpc ON lcp.id = lcpc.idJourney 
    LEFT JOIN learning_coursepath_users AS lcpu ON lcpu.idJourney = lcp.id 

    LEFT JOIN (
        SELECT idJourney, COUNT(DISTINCT idCourse) AS total
        FROM learning_coursepath_courses 
        GROUP BY idJourney
    ) AS journey_totals ON journey_totals.idJourney = lcp.id

    LEFT JOIN (
        SELECT lcpc.idJourney, user_reads.userid, COUNT(DISTINCT lcpc.idCourse) AS completed
        FROM learning_coursepath_courses AS lcpc
        
        JOIN (
            SELECT userid, idCourse, COUNT(DISTINCT id) AS completed_lessons
            FROM course_lesson_user 
            GROUP BY userid, idCourse
        ) AS user_reads ON user_reads.idCourse = lcpc.idCourse
        
        JOIN (
            SELECT idCourse, COUNT(DISTINCT id) AS total_lessons
            FROM course_learning_lessons 
            GROUP BY idCourse
        ) AS course_totals ON course_totals.idCourse = lcpc.idCourse AND user_reads.completed_lessons = course_totals.total_lessons GROUP BY lcpc.idJourney, user_reads.userid

    ) AS journey_reads 
        ON journey_reads.idJourney = lcp.id 
        AND journey_reads.userid = lcpu.userid 
        AND journey_reads.completed = journey_totals.total

    GROUP BY lcp.id
