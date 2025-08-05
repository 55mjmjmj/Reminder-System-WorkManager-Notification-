class ReminderWorker(context: Context, params: WorkerParameters) : Worker(context, params) {
    override fun doWork(): Result {
        NotificationUtils.showVitalsReminder(applicationContext)
        return Result.success()
    }
}


object NotificationUtils {
    fun showVitalsReminder(context: Context) {
        val intent = Intent(context, MainActivity::class.java).apply {
            flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK
        }
        val pendingIntent = PendingIntent.getActivity(context, 0, intent, PendingIntent.FLAG_IMMUTABLE)

        val notification = NotificationCompat.Builder(context, "VitalsChannel")
            .setSmallIcon(R.drawable.ic_notification)
            .setContentTitle("Time to log your vitals!")
            .setContentText("Stay on top of your health. Please update your vitals now!")
            .setContentIntent(pendingIntent)
            .setAutoCancel(true)
            .build()

        val manager = NotificationManagerCompat.from(context)
        manager.notify(1, notification)
    }
}


private fun scheduleReminder() {
    val workRequest = PeriodicWorkRequestBuilder<ReminderWorker>(5, TimeUnit.HOURS).build()
    WorkManager.getInstance(this).enqueueUniquePeriodicWork(
        "VitalsReminderWork",
        ExistingPeriodicWorkPolicy.KEEP,
        workRequest
    )
}




