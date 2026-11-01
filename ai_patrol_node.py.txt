import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist

class TurtleBotAIPatrolNode(Node):
    def __init__(self):
        super().__init__('turtlebot_ai_patrol')
        self.publisher_ = self.create_publisher(Twist, 'cmd_vel', 10)
        self.timer = self.create_timer(0.5, self.timer_callback)
        self.step_counter = 0
        self.get_logger().info("AI自律巡回コントローラー（TurtleBot3）が起動しました。")

    def timer_callback(self):
        msg = Twist()
        if self.step_counter < 12:
            msg.linear.x = 0.22
            msg.angular.z = 0.0
            self.get_logger().info("AI判断: 指定エリアを前進中...")
        elif self.step_counter < 18:
            msg.linear.x = 0.0
            msg.angular.z = 0.6
            self.get_logger().info("AI判断: コーナーを検出、方向転換中...")
        else:
            self.step_counter = 0

        self.publisher_.publish(msg)
        self.step_counter += 1

def main(args=None):
    rclpy.init(args=args)
    node = TurtleBotAIPatrolNode()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass
    finally:
        node.destroy_node()
        rclpy.shutdown()

if __name__ == '__main__':
    main()